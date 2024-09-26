ARG tag=latest
FROM gitlab.lrz.de:5005/00000000014afbe9/noise-project-bodensee/base_image${tag}

ENV NODE_VERSION=20.5.1
ENV NVM_DIR=/root/.nvm
ENV PATH="${NVM_DIR}/versions/node/v${NODE_VERSION}/bin:${PATH}"

# Install required packages and dependencies
RUN apt-get update && \
    apt-get install -y \
    openssl \
    libssl-dev \
    lsof

# Install Node.js and NPM
RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash && \
    . "$NVM_DIR/nvm.sh" && \
    nvm install ${NODE_VERSION} && \
    nvm use v${NODE_VERSION} && \
    nvm alias default v${NODE_VERSION} && \
    npm install -g pnpm@8.10.5

# Clone the docs repository and checkout a specific commit
WORKDIR /remix
RUN git init && \
    git remote add origin https://github.com/remix-run/remix.git && \
    git fetch --depth 1 origin 89eb3cf95078bfea2262cc6ad5839783b4b9e477 && \
    git checkout FETCH_HEAD

# Install dependencies and build the project
WORKDIR /remix
RUN pnpm install && pnpm run build
RUN npx playwright install-deps
RUN npx playwright install

RUN sed -i "s/process.env.CI ? 3 : 0/0/" /remix/integration/playwright.config.ts
RUN cat /remix/integration/playwright.config.ts

# Copy the test script to the container
COPY --chmod=0755 test.sh /test.sh

# Run the test script
ENV EXECUTIONS=100
CMD ["/bin/bash", "-c", "/test.sh $EXECUTIONS"]

