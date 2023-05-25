FROM node:18 as web

RUN yarn cache clean

RUN mkdir /dist
WORKDIR /dist

COPY package*.json .

# COPY yarn.lock .
# COPY .yarn .yarn
# COPY .yarnrc.yml .yarnrc.yml

COPY ./web/package*.json ./web/

RUN yarn install

COPY . .

# prerendering repends on having a db up - @rainkinz
RUN yarn rw build web --prerender false

# Use a multi-stage docker build
FROM nginx AS builder
COPY --from=web /dist /dist
# Remove the sourcemap files from the builder stage so this layer isn't in the final image
RUN find /dist -name '*.map' -delete
# Same with license files
RUN find /dist -name '*.LICENSE.txt' -delete

FROM nginx AS publish
# Copy from the builder stage so we have a clean image
COPY --from=builder /dist /usr/share/nginx/html
# COPY ./Dockerfiles/leftlane-mmentele-example/web/nginx.conf.template /etc/nginx/templates/default.conf.template
