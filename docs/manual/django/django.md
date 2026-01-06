# Django ORM Performance (Concise)

Practical patterns to keep queries fast and predictable.

## Avoid N+1
```python
qs = User.objects.select_related("profile")
qs = Author.objects.prefetch_related("books")
```

## Choose the Right Loader
- `select_related` for FK/OneToOne.
- `prefetch_related` for reverse FK/M2M.
- Use `Prefetch` to customize subqueries.

## Only Load What You Need
```python
qs = User.objects.only("id", "username")
qs = User.objects.defer("bio", "avatar")
```

## Use Database Work, Not Python
```python
from django.db.models import Count, Avg
qs = Author.objects.annotate(book_count=Count("books"))
```

## Exists / Values
```python
User.objects.filter(email=email).exists()
User.objects.values_list("id", flat=True)
```

## Pagination
```python
qs = Item.objects.order_by("-id")[:50]
```

## Indexes
- Add indexes for hot filters and sort columns.
- Use composite indexes for common multi-column filters.

## Transactions
```python
from django.db import transaction
with transaction.atomic():
    ...
```

## Debugging
- Use Django Debug Toolbar in dev.
- Log slow queries in production.
