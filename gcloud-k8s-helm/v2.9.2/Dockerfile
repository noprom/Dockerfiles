FROM google/cloud-sdk:206.0.0-alpine

MAINTAINER Tyee Noprom <tyeenoprom@gmail.com>

RUN apk update \
  && apk add ca-certificates openssl git openssh-client bash zip vim \
  && rm -rf /var/cache/apk/*

ENV HELM_VERSION v2.9.3
ENV HELM_GCS_PLUGIN_VERSION 0.1.4

RUN gcloud components install docker-credential-gcr -q --no-user-output-enabled
RUN gcloud components install kubectl -q --no-user-output-enabled
RUN gcloud components install gsutil -q --no-user-output-enabled
RUN curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get > get_helm.sh
RUN chmod 700 get_helm.sh
RUN ./get_helm.sh --version $HELM_VERSION

RUN helm init --client-only
RUN helm plugin install https://github.com/nouney/helm-gcs --version $HELM_GCS_PLUGIN_VERSION
