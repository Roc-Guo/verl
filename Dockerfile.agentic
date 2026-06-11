FROM verlai/verl:vllm017.latest

WORKDIR /home/verl
ENV PYTHONPATH="/home/verl:$PYTHONPATH"

COPY . .

RUN git clone https://github.com/alibaba/harbor.git /home/verl/harbor
RUN git clone -b feat/add-agentic-training-example https://github.com/alibaba/verl-recipe.git /home/verl/verl-recipe

RUN if [ ! -d /home/verl/recipe/agentic ]; then rm -rf /home/verl/recipe && ln -s /home/verl/verl-recipe /home/verl/recipe; fi

ENV VERSION=v0.20.2
RUN curl -L "https://github.com/google/go-containerregistry/releases/download/${VERSION}/go-containerregistry_Linux_x86_64.tar.gz" -o crane.tar.gz \
    && tar -xzf crane.tar.gz && mv crane /usr/local/bin/ \
    && rm -f crane.tar.gz

ENV BUILDKIT_VERSION=v0.13.2
RUN curl -sL "https://github.com/moby/buildkit/releases/download/${BUILDKIT_VERSION}/buildkit-${BUILDKIT_VERSION}.linux-amd64.tar.gz" \
    | tar -xz -C /usr/local bin/buildctl

RUN pip install kubernetes oauthlib
RUN pip install -e /home/verl/harbor

RUN apt update && apt install -y openssh-server vim
RUN apt remove python3-blinker -y; pip install -U "ray[data,train,tune,serve]"; pip install -e .

RUN python3 -c "import ray; print('ray ok')" \
    && python3 -c "import kubernetes; print('kubernetes sdk ok')" \
    && command -v crane \
    && command -v buildctl \
    && ls -la /home/verl/recipe/agentic
