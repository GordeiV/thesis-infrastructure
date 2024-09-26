ARG tag=latest
FROM gitlab.lrz.de:5005/00000000014afbe9/noise-project-bodensee/base_image${tag}

ENV NODE_VERSION=20.9.0
ENV NVM_DIR=/root/.nvm
ENV PATH="${NVM_DIR}/versions/node/v${NODE_VERSION}/bin:${PATH}"


# Install Node.js and NPM
RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash && \
    . "$NVM_DIR/nvm.sh" && \
    nvm install ${NODE_VERSION} && \
    nvm use v${NODE_VERSION} && \
    nvm alias default v${NODE_VERSION} && \
    npm install -g npm

# Clone the nextjs repository and checkout a specific commit 
WORKDIR /nextjs
RUN git init && \
    git remote add origin https://github.com/vercel/next.js.git && \
    git fetch --depth 1 origin ca10b73cd5e32b00adcec50afc0855548d41b309 && \
    git checkout FETCH_HEAD

RUN npm install -g pnpm@8.15.4
RUN pnpm install
RUN pnpm playwright install

RUN pnpm clean

RUN pnpm build

COPY --chmod=0755 test.sh /test.sh

RUN npx playwright install-deps
ENV NEXT_TEST_CONTINUE_ON_ERROR=true
ENV EXECUTIONS=25
CMD ["/bin/bash", "-c", "/test.sh $EXECUTIONS"]

