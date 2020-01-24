FROM openfaas/of-watchdog:0.7.6 as watchdog

FROM node as build

RUN apt-get update && apt-get install -y build-essential && apt-get install -y python && npm install

WORKDIR /usr/api

COPY ./package.json  .

RUN yarn install --production



FROM mhart/alpine-node:latest

WORKDIR /usr/api

COPY --from=watchdog /fwatchdog /usr/bin/fwatchdog
RUN chmod +x /usr/bin/fwatchdog

# Add non root user
RUN addgroup -S api && adduser api -S -G api
RUN chown api /usr/api


COPY --from=build /usr/api . 
COPY ./.next .next

USER api

# Populate example here - i.e. "cat", "sha512sum" or "node index.js"
ENV fprocess="yarn start"
# Set to true to see request in function logs
ENV write_debug="false"
ENV mode="http"
ENV upstream_url="http://127.0.0.1:3000"

ENV exec_timeout="10s"
ENV write_timeout="15s"
ENV read_timeout="15s"


HEALTHCHECK --interval=3s CMD [ -e /tmp/.lock ] || exit 1

CMD ["fwatchdog"]
