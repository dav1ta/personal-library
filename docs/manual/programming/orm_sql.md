
# Advanced ORM Tutorial: Django, SQLAlchemy & Raw SQL

## 1. Filtering Records with Conditional Logic

### Django ORM:
```
from django.models import Q
YourModel.objects.filter(Q(field1="value1") | Q(field2="value2"))
```

### SQLAlchemy:
```
from sqlalchemy import or_
session.query(YourModel).filter(or_(YourModel.field1 == 'value1', YourModel.field2 == 'value2'))
```

### Raw SQL:
```
SELECT * FROM your_model WHERE field1 = 'value1' OR field2 = 'value2';
```

## 2. Aggregating Data

### Django ORM:
```
from django.db.models import Sum
YourModel.objects.aggregate(Sum('field1'))
```

### SQLAlchemy:
```
from sqlalchemy import func
session.query(func.sum(YourModel.field1)).scalar()
```

### Raw SQL:
```
SELECT SUM(field1) FROM your_model;
```

## 3. Joining Tables

### Django ORM:
```
YourModel.objects.select_related('related_model')
```

### SQLAlchemy:
```
session.query(YourModel, RelatedModel).join(RelatedModel)
```

### Raw SQL:
```
SELECT * FROM your_model JOIN related_model ON your_model.related_id = related_model.id;
```

## 4. Grouping Records

### Django ORM:
```
from django.db.models import Count
YourModel.objects.values('field1').annotate(Count('field2'))
```

### SQLAlchemy:
```
from sqlalchemy import func
session.query(YourModel.field1, func.count(YourModel.field2)).group_by(YourModel.field1)
```

### Raw SQL:
```
SELECT field1, COUNT(field2) FROM your_model GROUP BY field1;
```

## 5. Complex Subqueries

### Django ORM:
```
subquery = YourModel.objects.filter(field1="value1").values('field2')
result = OtherModel.objects.filter(field3__in=subquery)
```

### SQLAlchemy:
```
subquery = session.query(YourModel.field2).filter(YourModel.field1 == 'value1').subquery()
result = session.query(OtherModel).filter(OtherModel.field3.in_(subquery))
```

### Raw SQL:
```
SELECT * FROM other_model WHERE field3 IN (SELECT field2 FROM your_model WHERE field1 = 'value1');
```

## 6. Limiting and Offsetting Results

### Django ORM:
```
YourModel.objects.all()[5:10]
```

### SQLAlchemy:
```
session.query(YourModel).limit(5).offset(5)
```

### Raw SQL:
```
SELECT * FROM your_model LIMIT 5 OFFSET 5;
```

## 7. Transactions

### Django ORM:
```
from django.db import transaction

with transaction.atomic():
    YourModel.objects.create(field1="value1")
    YourModel.objects.create(field1="value2")
```

### SQLAlchemy:
```
with session.begin():
    new_record1 = YourModel(field1="value1")
    new_record2 = YourModel(field1="value2")
    session.add(new_record1)
    session.add(new_record2)
```

### Raw SQL:
```
BEGIN;
INSERT INTO your_model (field1) VALUES ('value1');
INSERT INTO your_model (field1) VALUES ('value2');
COMMIT;
```

## 8. Custom Fields & Expressions

### Django ORM:
```
from django.db.models import F, ExpressionWrapper, IntegerField
YourModel.objects.annotate(new_field=ExpressionWrapper(F('field1') + F('field2'), output_field=IntegerField()))
```

### SQLAlchemy:
```
from sqlalchemy import func
session.query(YourModel, (YourModel.field1 + YourModel.field2).label('new_field'))
```

### Raw SQL:
```
SELECT *, (field1 + field2) AS new_field FROM your_model;
```

## 9. Case and Conditional Expressions

### Django ORM:
```
from django.db.models import Case, When, Value, CharField
YourModel.objects.annotate(
    field_status=Case(
        When(field1="value1", then=Value('status1')),
        When(field1="value2", then=Value('status2')),
        default=Value('unknown'),
        output_field=CharField()
    )
)
```

### SQLAlchemy:
```
from sqlalchemy.sql.expression import case
session.query(YourModel,
    case([
        (YourModel.field1 == "value1", "status1"),
        (YourModel.field1 == "value2", "status2"),
    ], else_="unknown").label('field_status')
)
```

### Raw SQL:
```
SELECT *,
    CASE
        WHEN field1 = 'value1' THEN 'status1'
        WHEN field1 = 'value2' THEN 'status2'
        ELSE 'unknown'
    END AS field_status
FROM your_model;
```

## 10. Raw SQL in ORM

### Django ORM:
```
YourModel.objects.raw('SELECT * FROM your_model WHERE field1 = %s', ['value1'])
```

### SQLAlchemy:
```
session.query(YourModel).from_statement("SELECT * FROM your_model WHERE field1 = :value").params(value="value1")
```

### Raw SQL:
```
SELECT * FROM your_model WHERE field1 = 'value1';
```



# Advanced ORM Optimization Techniques: Django, SQLAlchemy & Raw SQL

## 1. Avoiding `n+1` Queries Problem

### Django ORM:
```
# Using select_related for ForeignKey and OneToOneField relations
YourModel.objects.select_related('related_model').all()

# Using prefetch_related for ManyToManyField and reverse ForeignKey relations
YourModel.objects.prefetch_related('related_model_set').all()
```

### SQLAlchemy:
```
# Using joinedload for JOINed loading
from sqlalchemy.orm import joinedload
session.query(YourModel).options(joinedload(YourModel.related_model))
```

### Raw SQL:
```
SELECT * FROM your_model 
JOIN related_model ON your_model.related_id = related_model.id;
```

## 2. Only Fetch What You Need

### Django ORM:
```
YourModel.objects.only('field1', 'field2')
```

### SQLAlchemy:
```
session.query(YourModel.field1, YourModel.field2)
```

### Raw SQL:
```
SELECT field1, field2 FROM your_model;
```

## 3. Using Database Indexes

### Django ORM:
```
# When defining the model, use db_index=True
class YourModel(models.Model):
    field1 = models.CharField(max_length=100, db_index=True)
```

### SQLAlchemy:
```
# Define the index within the table
from sqlalchemy import Index, create_engine, MetaData

meta = MetaData()
your_table = Table('your_model', meta,
    Column('field1', String, index=True)
)
```

### Raw SQL:
```
CREATE INDEX index_name ON your_model (field1);
```

## 4. Batch Inserts

### Django ORM:
```
# Using bulk_create
YourModel.objects.bulk_create([YourModel(field1=value) for value in value_list])
```

### SQLAlchemy:
```
session.bulk_insert_mappings(YourModel, [{'field1': value} for value in value_list])
```

### Raw SQL:
```
INSERT INTO your_model (field1) VALUES (value1), (value2), ...;
```

## 5. Optimizing Count Queries

### Django ORM:
```
YourModel.objects.filter(some_criteria=True).count()
```

### SQLAlchemy:
```
session.query(func.count(YourModel.id)).filter(YourModel.some_criteria == True)
```

### Raw SQL:
```
SELECT COUNT(id) FROM your_model WHERE some_criteria = TRUE;
```

## 6. Use EXISTS for Presence Checks

### Django ORM:
```
if YourModel.objects.filter(field1=value).exists():
    # Do something
```

### SQLAlchemy:
```
if session.query(YourModel).filter(YourModel.field1 == value).limit(1).first():
    # Do something
```

### Raw SQL:
```
SELECT EXISTS(SELECT 1 FROM your_model WHERE field1 = value);
```

## 7. Use Database Functions for Computation

### Django ORM:
```
from django.db.models.functions import Coalesce
YourModel.objects.update(field2=Coalesce('field2', 0) + 1)
```

### SQLAlchemy:
```
from sqlalchemy.sql.expression import func
session.query(YourModel).update({YourModel.field2: func.coalesce(YourModel.field2, 0) + 1})
```

### Raw SQL:
```
UPDATE your_model SET field2 = COALESCE(field2, 0) + 1;
```

## 8. Avoiding ORM Overhead for Large Data Sets

### Django ORM:
```
YourModel.objects.values('field1', 'field2')
```

### SQLAlchemy:
```
session.query(YourModel.field1, YourModel.field2).yield_per(1000)
```

### Raw SQL:
```
SELECT field1, field2 FROM your_model;
```

## 9. De-normalization for Faster Reads

### Django ORM:
```
# Using annotated fields or computed properties
class YourModel(models.Model):
    field1 = models.IntegerField()
    field2 = models.IntegerField()
    total = models.IntegerField(editable=False)

    def save(self, *args, **kwargs):
        self.total = self.field1 + self.field2
        super().save(*args, **kwargs)
```

### SQLAlchemy:
```
from sqlalchemy import Column, Integer, event

class YourModel(Base):
    __tablename__ = 'your_model'

    id = Column(Integer, primary_key=True)
    field1 = Column(Integer)
    field2 = Column(Integer)
    total = Column(Integer)

@event.listens_for(YourModel, 'before_insert')
def before_insert(mapper, connection, target):
    target.total = target.field1 + target.field2
```

### Raw SQL:
```
-- Assuming the total column is already added to your_model
UPDATE your_model SET total = field1 + field2;
```

## 10. Caching Expensive Queries

### Django ORM:
```
# Using Django's cache framework
from django.core.cache import cache

key = "your_cache_key"
data = cache.get(key)

if data is None:
    data = YourModel.objects.filter(some_criteria=True)
    cache.set(key, data)
```

### SQLAlchemy:
```
# Using dogpile.cache for SQLAlchemy
from dogpile.cache import make_region

region = make_region().configure('dogpile.cache.memory', expiration_time=3600)
key = "your_cache_key"
data = region.get(key)

if data is None:
    data = session.query(YourModel).filter(YourModel.some_criteria == True).all()
    region.set(key, data)
```

### Raw SQL:
```
-- This varies by the database and specific caching solutions. Typically, databases have their internal caching mechanisms for frequent queries.

# Deep Dive into Advanced ORM Techniques: Django, SQLAlchemy & Raw SQL

## 11. Composite Primary Keys

### Django ORM:
```
# Django does not natively support composite primary keys. However, you can use a unique constraint.
class YourModel(models.Model):
    field1 = models.IntegerField()
    field2 = models.IntegerField()
    
    class Meta:
        constraints = [
            models.UniqueConstraint(fields=['field1', 'field2'], name='unique_field1_field2')
        ]
```

### SQLAlchemy:
```
from sqlalchemy import Column, Integer, PrimaryKeyConstraint

class YourModel(Base):
    __tablename__ = 'your_model'
    
    field1 = Column(Integer)
    field2 = Column(Integer)
    
    __table_args__ = (
        PrimaryKeyConstraint('field1', 'field2'),
    )
```

### Raw SQL:
```
CREATE TABLE your_model (
    field1 INT,
    field2 INT,
    PRIMARY KEY (field1, field2)
);
```

## 12. Using Views

### Django ORM:
```
# Create a view in your database, then create a model mapped to this view. Ensure db_table points to the view.
class YourViewModel(models.Model):
    field1 = models.IntegerField()
    field2 = models.IntegerField()
    
    class Meta:
        managed = False  # Django will not manage this table
        db_table = 'your_view'
```

### SQLAlchemy:
```
# Map the model to an existing view.
class YourViewModel(Base):
    __tablename__ = 'your_view'
    field1 = Column(Integer, primary_key=True)
    field2 = Column(Integer)
```

### Raw SQL:
```
CREATE VIEW your_view AS
SELECT field1, field2 FROM your_model WHERE some_criteria = TRUE;
```

## 13. Temporary Tables

### Django ORM:
```
# Django ORM doesn't have built-in support for temporary tables. You'd typically create them using raw SQL.
from django.db import connection

with connection.cursor() as cursor:
    cursor.execute('''
        CREATE TEMPORARY TABLE temp_your_model AS
        SELECT * FROM your_model WHERE some_criteria = TRUE;
    ''')
```

### SQLAlchemy:
```
# Use the standard table creation but specify it as a temporary table.
temp_table = Table(
    "temp_your_model", metadata,
    Column('field1', Integer),
    # Add other columns...
    prefixes=['TEMPORARY']
)
temp_table.create(bind=engine)
```

### Raw SQL:
```
CREATE TEMPORARY TABLE temp_your_model AS
SELECT * FROM your_model WHERE some_criteria = TRUE;
```

## 14. Recursive Queries (Common Table Expressions)

### Django ORM:
```
# Django 3.1 introduced support for recursive CTEs
from django_cte import CTEManager, CTEQuerySet

class YourModel(models.Model):
    parent = models.ForeignKey('self', null=True, on_delete=models.CASCADE)
    name = models.CharField(max_length=200)
    
    objects = CTEManager.from_queryset(CTEQuerySet)()

with YourModel.objects.with_cte(recursive=True) as cte:
    cte_qs = cte.queryset.annotate(level=models.Value(0)).filter(name="root_name")
    children_qs = cte.queryset.filter(parent=cte.join()).annotate(level=cte.col.level + 1)
    cte.union(cte_qs, children_qs)
    results = cte.all()
```

### SQLAlchemy:
```
from sqlalchemy import select, union_all
from sqlalchemy.orm import aliased

descendants = select([
    YourModel.id, YourModel.parent_id, YourModel.name
]).where(YourModel.name == 'root_name').cte(name='descendants', recursive=True)

parent_alias = aliased(descendants)
children = select([
    YourModel.id, YourModel.parent_id, YourModel.name
]).join(
    parent_alias, parent_alias.c.id == YourModel.parent_id
)

descendants = descendants.union_all(children)
session.query(descendants).all()
```

### Raw SQL:
```
WITH RECURSIVE descendants AS (
    SELECT id, parent_id, name
    FROM your_model WHERE name = 'root_name'
    
    UNION ALL
    
    SELECT m.id, m.parent_id, m.name
    FROM your_model m
    JOIN descendants d ON d.id = m.parent_id
)
SELECT * FROM descendants;
```

## 15. Upserts (Insert or Update)

### Django ORM:
```
from django.db import IntegrityError

try:
    YourModel.objects.create(id=some_id, field1=value1)
except IntegrityError:
    YourModel.objects.filter(id=some_id).update(field1=value1)
```

### SQLAlchemy:
```
from sqlalchemy.dialects.postgresql import insert

stmt = insert(YourModel).values(id=some_id, field1=value1)
stmt = stmt.on_conflict_do_update(
    index_elements=['id'],
    set_=dict(field1=value1)
)
session.execute(stmt)
```

### Raw SQL:
```
INSERT INTO your_model (id, field1)
VALUES (some_id, 'value1')
ON CONFLICT (id) DO UPDATE
SET field1 = 'value1';
```

---

These are some deeper techniques and features that can be utilized in ORMs and SQL to optimize, enhance, and leverage powerful database features. Remember that the most suitable technique always depends on the specific problem you're solving, the database you're using, and the scale at which you operate.

Next: [Graph](../graph/graph.md)
