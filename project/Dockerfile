### --- Development ---
ARG IMAGE_NAME=node
ARG IMAGE_TAG=22.11.0
FROM ${IMAGE_NAME}:${IMAGE_TAG} AS development

# Source working/installation directory
ENV INSTALL=/usr/src/app
WORKDIR ${INSTALL}

COPY ./certs/tls-ca-bundle.pem /etc/pki/ca-trust/extracted/pem/tls-ca-bundle.pem
ENV NODE_EXTRA_CA_CERTS=/etc/pki/ca-trust/extracted/pem/tls-ca-bundle.pem

# Copy package.json and package-lock.json to the working directory
COPY --chown=node:node package*.json ./

# Install the application dependencies
RUN npm clean-install \
  && npm install -g @nestjs/cli

# Copy the rest of the application files
COPY --chown=node:node . .

# Build the NestJS application
RUN npm run build

# Expose the application port
EXPOSE 3000

# Command to run the application
CMD [ "npm", "run", "start:dev" ]

### --- Production ---

#### --- BUILD STAGE ---
FROM ${IMAGE_NAME}:${IMAGE_TAG} AS build
# Source working/installation directory
ENV INSTALL=/usr/src/app
WORKDIR ${INSTALL}
COPY --chown=node:node package*.json ./
COPY --chown=node:node --from=development /usr/src/app/node_modules ./node_modules
COPY --chown=node:node . .
RUN npm run build
RUN npm clean-install -f --only=production && npm cache clean --force
USER node

### --- DEPLOY STAGE ---
FROM ${IMAGE_NAME}:${IMAGE_TAG} AS production
ENV NODE_ENV=production

ARG MAINTAINER=""
ARG APP_NAME=""
ARG DESC=""
ARG APP_VERSION=""
ARG BUILD_DATE=""
ARG DOCS=""
ARG LICENSES=""
ARG REF_NAME=${IMAGE_NAME}:${IMAGE_TAG}
ARG REVISION=""
ARG SOURCE=""
ARG URL=""
ARG VENDOR=""

LABEL \
  org.opencontainers.image.authors=${MAINTAINER} \
  org.opencontainers.image.created=${BUILD_DATE} \
  org.opencontainers.image.description=${DESC} \
  org.opencontainers.image.documentation=${DOCS} \
  org.opencontainers.image.licenses=${LICENSES} \
  org.opencontainers.image.ref.name=${REF_NAME} \
  org.opencontainers.image.revision=${REVISION} \
  org.opencontainers.image.source=${SOURCE} \
  org.opencontainers.image.title=${APP_NAME} \
  org.opencontainers.image.url=${URL} \
  org.opencontainers.image.vendor=${VENDOR} \
  org.opencontainers.image.version=${APP_VERSION}

RUN echo '[ ! -z "${TERM}" -a -r /etc/motd ] && cat /etc/issue && cat /etc/motd' \
  >> /etc/bash.bashrc \
  ; echo "\
  ===================================================================\n\
  = ${APP_NAME} Docker container : ${APP_VERSION}                       =\n\
  ===================================================================\n\
  \n\
  ${APP_NAME}: ${DESC}\n\
  (c) ${MAINTAINER} - ${BUILD_DATE}\n\
  \n\
  Source directory is ${INSTALL}\n"\
  > /etc/motd
# Source working/installation directory
ENV INSTALL=/usr/src/app
WORKDIR ${INSTALL}
COPY --chown=node:node --from=build ${INSTALL}/node_modules ./node_modules
COPY --chown=node:node --from=build ${INSTALL}/dist ./dist
CMD [ "node", "dist/main.js" ]