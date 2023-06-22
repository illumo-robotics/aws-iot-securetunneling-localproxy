ARG OS
ARG BASE_IMAGE
FROM ${BASE_IMAGE} AS deploy

###############################################################################
# Copy and build local proxy
###############################################################################
COPY . /root/aws-iot-securetunneling-localproxy
RUN mkdir -p /root/aws-iot-securetunneling-localproxy/build \
    && cd /root/aws-iot-securetunneling-localproxy/build \
    && cmake .. \
    && make

FROM ${OS} AS minimum_size


RUN apt update && apt upgrade -y && \
	apt install -y wget 

RUN mkdir -p /home/aws-iot-securetunneling-localproxy/certs && \
    cd /home/aws-iot-securetunneling-localproxy/certs && \
    wget https://www.amazontrust.com/repository/AmazonRootCA1.pem && \
	openssl rehash ./

COPY --from=deploy /root/aws-iot-securetunneling-localproxy/build/bin/localproxy /root/bin/localproxy

COPY ./.github/docker-images/oss-compliance /root/oss-compliance
RUN HOME_DIR=/root \
    && cd ${HOME_DIR}/oss-compliance \
    && chmod +x ${HOME_DIR}/oss-compliance/generate-oss-compliance.sh \
    && chmod +x ${HOME_DIR}/oss-compliance/test/test-oss-compliance.sh \
    && bash ${HOME_DIR}/oss-compliance/generate-oss-compliance.sh ${HOME_DIR} \
    && rm -rf ${HOME_DIR}/oss-compliance*

WORKDIR /root/bin

# ENTRYPOINT ["/root/bin/localproxy"]