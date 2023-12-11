kubectl create ns microservice
helm install  frontend frontend --values frontend/values.yaml
helm install  backend backend --values backend/values.yaml
helm install  db db --values db/values.yaml


helm template  frontend-tier frontend-tier --values frontend-tier/values.yaml
helm template  backend-tier backend-tier --values backend-tier/values.yaml
helm template  db-tier backend-tier --values db-tier/values.yaml

mongo_db_username: mg-admin
mongo_db_password: mongodb




helm uninstall  db-tier 
helm uninstall backend-tier



helm template  orders orders --values orders/values.yaml
