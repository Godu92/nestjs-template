ARG IMAGE_NAME=node
ARG IMAGE_TAG=20.18.1
FROM ${IMAGE_NAME}:${IMAGE_TAG}

## Add these lines to dockerfiles for consistent labeling
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

# Source working/installation directory
ENV INSTALL=/usr/src/app
WORKDIR ${INSTALL}
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


# Copy package.json and package-lock.json to the working directory
COPY package*.json ./

# Install the application dependencies
RUN npm install

# Copy the rest of the application files
COPY . .

# Build the NestJS application
RUN npm run build

# Expose the application port
EXPOSE 3000

# Command to run the application
CMD [ "npm", "run", "start:dev" ]