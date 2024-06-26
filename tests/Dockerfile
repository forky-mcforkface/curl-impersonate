ARG FIREFOX_IMAGE=curl-impersonate-ff
ARG CHROME_IMAGE=curl-impersonate-chrome

FROM ${FIREFOX_IMAGE} as ff
FROM ${CHROME_IMAGE} as chrome

FROM python:3.10.1-slim-buster

WORKDIR /tests

RUN apt-get update && \
    apt-get install -y tcpdump libbrotli1 libnss3 gcc libcurl4-openssl-dev nghttp2-server

COPY requirements.txt requirements.txt

RUN pip install --upgrade pip && \
    pip install -r requirements.txt

# Copy the built binaries from both containers
COPY --from=ff /usr/local/ /usr/local/
COPY --from=chrome /usr/local/ /usr/local/

# Needed to update the loader's cache
RUN ldconfig

COPY . .

# Compile 'minicurl' which is used for testing libcurl-impersonate.
# 'minicurl' is compiled against the "regular" libcurl.
# libcurl-impersonate will replace it at runtime via LD_PRELOAD.
RUN gcc -Wall -Werror -o minicurl minicurl.c `curl-config --libs`
RUN install minicurl /usr/local/bin

ENTRYPOINT ["pytest"]
