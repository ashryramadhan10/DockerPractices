FROM alpine:3

LABEL author="ashry ramadhan"
LABEL company="<none>" website="<none>"

RUN mkdir -p /hello \
    && cd /hello \
    && touch hello-world.txt \
    && echo "Hello World" > hello-world.txt && cd ../

ADD ./texts/*.txt /hello

# CMD cat hello-world.txt