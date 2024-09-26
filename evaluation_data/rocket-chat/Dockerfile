ARG tag=latest
FROM gitlab.lrz.de:5005/00000000014afbe9/noise-project-bodensee/base_image${tag}

ENV NODE_VERSION=14.21.3
ENV NVM_DIR=/root/.nvm
ENV PATH="${NVM_DIR}/versions/node/v${NODE_VERSION}/bin:${PATH}"

# Install required packages and dependencies
RUN apt-get update && \
    apt-get install -y \
    g++ build-essential git curl python2-minimal npm libnss3 libnspr4 libgbm1 libasound2

RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash && \
    . "$NVM_DIR/nvm.sh" && \
    nvm install ${NODE_VERSION} && \
    nvm use v${NODE_VERSION} && \
    nvm alias default v${NODE_VERSION} && \
    npm install --g yarn

RUN corepack prepare yarn@3.5.0

# Clone the material-ui repository and checkout a specific commit
WORKDIR /rocket-chat
RUN git init && \
    git remote add origin https://github.com/RocketChat/Rocket.Chat.git && \
    git fetch --depth 1 origin a899d410e283a18d03efcad526b7cb4e1d46962f && \
    git checkout FETCH_HEAD

WORKDIR /rocket-chat
RUN yarn
RUN yarn build

RUN curl https://install.meteor.com/ | sh

# Remove maxFailures from playwright.config.ts
RUN sed -i '/maxFailures: process.env.CI ? 5 : undefined,/d' apps/meteor/playwright.config.ts
RUN sed -i '/retries: parseInt(String(process.env.PLAYWRIGHT_RETRIES)) || 0,/d' apps/meteor/playwright.config.ts
RUN cat apps/meteor/playwright.config.ts

# Copy the test script to the container
COPY --chmod=0755 test.sh /test.sh

WORKDIR /rocket-chat/apps/meteor
RUN yarn playwright install

# Run the test script
ENV EXECUTIONS=16
WORKDIR /rocket-chat
CMD ["/bin/bash", "-c", "/test.sh $EXECUTIONS"]

