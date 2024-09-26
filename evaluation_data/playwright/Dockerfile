ARG tag=latest
FROM gitlab.lrz.de:5005/00000000014afbe9/noise-project-bodensee/base_image${tag}

# require Node.js 16+ and NPM 8+
ENV NODE_VERSION=18.20.1
ENV NVM_DIR=/root/.nvm
ENV PATH="${NVM_DIR}/versions/node/v${NODE_VERSION}/bin:${PATH}"

# Install required packages and dependencies
RUN apt-get update && \
    apt-get install -y xvfb

# Install Node.js and NPM
RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash && \
    . "$NVM_DIR/nvm.sh" && \
    nvm install ${NODE_VERSION} && \
    nvm use v${NODE_VERSION} && \
    nvm alias default v${NODE_VERSION} && \
    npm i -g npm@latest

# Clone the playwright repository and checkout a specific commit
WORKDIR /playwright
RUN git init && \
    git remote add origin https://github.com/microsoft/playwright.git && \
    git fetch --depth 1 origin 7afd4170ea15bac8f43d1be1789b28f68b708b80 && \
    git checkout FETCH_HEAD

# Disable retries
RUN sed -i '/retries: process.env.CI ? 3 : 0/d' /playwright/tests/library/playwright.config.ts
RUN cat /playwright/tests/library/playwright.config.ts

# Install dependencies and build the project
WORKDIR /playwright
RUN npm ci && \
    npm run build && \
    npx playwright install --with-deps chromium chromium

# Copy the test script to the container
COPY --chmod=0755 test.sh /test.sh

# Run the test script
ENV EXECUTIONS=100
CMD ["/bin/bash", "-c", "/test.sh $EXECUTIONS"]

