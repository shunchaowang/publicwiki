This is a `tiktok` clone using NextJs as the backend framework and react as the frontend framework.
I use typescript, tailwind, Prisma, GraphQL and Apollo Client for GraphQL query. Zustand is used to manage the states
among the components.
# Bootstrap the backend and frontend
# Develop the backend
## Create database mapping
I use `prisma` as the OR mapping framework, run `npm i -D prisma` to install the dev dependency of prisma. After prisma
is installed, we will update the `prisma/schema.prisma` with the database provider and url. 
1. Edit `schema.prisma` to use `postgresql` as the provider
2. Create a docker file to run the postgres database in the docker
3. Add models to the `schema.prisma` file
- Create `User` model
- Create `Post` model, make sure to cascadely delete the posts when the user is deleted
- Create `Comment` model, make sure to cascadely delete the comments when the user is deleted
- Create `Like` model, make sure to cascadely delete the liks when the user is deleted
4. Add database connection info to `.env` file
## Set up GraphQL
## Enable cross origin 
## Create backend endpoints



