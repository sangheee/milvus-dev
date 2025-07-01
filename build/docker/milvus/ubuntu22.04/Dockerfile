# Copyright (C) 2019-2022 Zilliz. All rights reserved.
#
# Licensed under the Apache License, Version 2.0 (the "License"); you may not use this file except in compliance
# with the License. You may obtain a copy of the License at
#
# http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software distributed under the License
# is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express
# or implied. See the License for the specific language governing permissions and limitations under the License.

FROM ubuntu:jammy-20240530

ARG TARGETARCH
ARG MILVUS_ASAN_LIB

RUN apt-get update && \
    apt-get install -y --no-install-recommends curl ca-certificates libaio-dev libgomp1 libopenblas-dev gcc g++ && \
    apt-get remove --purge -y && \
    rm -rf /var/lib/apt/lists/*

# Add Tini
RUN curl -L -o /tini https://github.com/krallin/tini/releases/download/v0.19.0/tini-$TARGETARCH && \
    chmod +x /tini

RUN mkdir -p /milvus/bin
#COPY --chown=root:root --chmod=774 ./bin/milvus /milvus/bin/milvus
#COPY --chown=root:root --chmod=774 ./configs/ /milvus/configs/
#COPY --chown=root:root --chmod=774 ./lib/ /milvus/lib/
COPY ./bin/milvus /milvus/bin/milvus
COPY ./configs/ /milvus/configs/
COPY ./lib/ /milvus/lib/

# 2. RUN 명령어를 사용하여 소유권 변경 (chown)
# 이 예시에서는 root:root로 변경하므로 사실상 변경할 필요가 없지만,
# 다른 사용자/그룹으로 변경해야 한다면 여기에 명시합니다.
# 예: RUN chown -R myuser:mygroup /milvus/bin/milvus /milvus/configs/ /milvus/lib/
# 현재는 root:root로 설정하므로 이 단계는 생략 가능하거나
# RUN chown -R root:root /milvus/bin/milvus /milvus/configs/ /milvus/lib/ 와 같이 명시할 수 있습니다.
# (root:root가 기본 소유권이므로 이 명령어는 대부분 불필요합니다.)
RUN chown root:root /milvus/bin/milvus \
    && chown -R root:root /milvus/configs/ \
        && chown -R root:root /milvus/lib/

# 3. RUN 명령어를 사용하여 권한 변경 (chmod)
RUN chmod 774 /milvus/bin/milvus && chmod 774 /milvus/configs/ && chmod 774 /milvus/lib/

ENV PATH=/milvus/bin:$PATH
ENV LD_LIBRARY_PATH=/milvus/lib:$LD_LIBRARY_PATH:/usr/lib
ENV LD_PRELOAD=${MILVUS_ASAN_LIB}:/milvus/lib/libjemalloc.so
ENV MALLOC_CONF=background_thread:true

ENTRYPOINT ["/tini", "--"]

WORKDIR /milvus/
