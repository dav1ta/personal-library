## lifecycle

## rollout and versioning

when create deploiment it triggers rollout and it creates revision
when container updaited new revision is created. this helps tracking of changes
end gives ability to rollback


`kubectl rollout status deployment/myapp-deployment`

`kubectl rollout history deployment/myapp-deployment`


## deploiment strategies

### recreate
delete all and crate news

### rolling update
replace one by one

new replacasets will be created.

## update using kubectl apply

## kubectl set image == but not good idea

## Rollback

`kubectl rollout undo deployment/myapp-deployment`


## 
