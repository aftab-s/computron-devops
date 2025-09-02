#MICROSERVICES VS MONOLETHIC
###  Multicontainers architecture is called Docker Compose
if the 1 section is down We can Troubleshoot through the logs
### multicontainer architecture is Called microservice architecture
## ex:
amazon shopping site

frontend  --    dockerfile1 ---   dockerimage1
backend  --    dockerfile2 ---   dockerimage2
Dynamic db --    dockerfile3 ---   dockerimage3
Ai model --    dockerfile4 ---   dockerimage4
postgress,rds  --    dockerfile5 ---   dockerimage5

## Dockercompose -- df1+df2+df3+df4+df5+df6

### realcase
if dynamodb needs to change to mongodb,then payment gateway replica containers will work 
DF3 -> Df3.1+df3.2.. ....... . .. . .
multiple container running as instances

## Monolethic
whole entire application packaging and deploying a traditional,single-unit application within docker container

### Microservices vs Monolethic
| Feature              | Monolithic Architecture        | Microservices Architecture          |
| -------------------- | ------------------------------ | ----------------------------------- |
| **Codebase**         | Single, unified                | Multiple, independent services      |
| **Deployment**       | One package                    | Each service deployed separately    |
| **Scalability**      | Entire app scales              | Individual services scale           |
| **Technology stack** | Single stack                   | Multiple stacks possible            |
| **Database**         | One centralized DB             | Separate DB per service (or schema) |
| **Complexity**       | Simple to build, hard to scale | Complex to build, easy to scale     |
| **Best for**         | Small apps/startups            | Large, complex, enterprise apps     |
