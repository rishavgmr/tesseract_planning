ARG TAG
FROM ghcr.io/tesseract-robotics/trajopt:${TAG}
ENV E_TAG=$TAG

SHELL ["/bin/bash", "-c"]

ENV DEBIAN_FRONTEND noninteractive

USER root

# Install the dependency repositories
RUN apt update -y -qq \
  && if [[ $(lsb_release -cs) != noble ]] ; then \
    apt install -y --no-install-recommends software-properties-common \
    && add-apt-repository -y ppa:levi-armstrong/tesseract-robotics \
    && apt install -y libompl-dev taskflow \
  ; else \
    apt install -y libompl-dev libtaskflow-cpp-dev \
  ; fi

# Bind mount the source directory so as not to unnecessarily copy source code into the docker image
ARG WORKSPACE_DIR=/opt/tesseract_planning
RUN --mount=type=bind,target=${WORKSPACE_DIR}/src/tesseract_planning \
  cd ${WORKSPACE_DIR} \
  && vcs import src < src/tesseract_planning/dependencies.repos --shallow \
  && rosdep install \
    --from-paths ${WORKSPACE_DIR}/src \
    -iry

# Build the repository
# Bind mount the source directory so as not to unnecessarily copy source code into the docker image
RUN --mount=type=bind,target=${WORKSPACE_DIR}/src/tesseract_planning \
  source /opt/tesseract/install/setup.bash \
  && source /opt/trajopt/install/setup.bash \
  && cd ${WORKSPACE_DIR} \ 
  && colcon build --cmake-args -DCMAKE_BUILD_TYPE=Release \
  && rm -rf build log
