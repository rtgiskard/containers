ARG BASE_GOLANG
FROM $BASE_GOLANG AS builder

WORKDIR /go/src/
ENV GOPATH /go

RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.ustc.edu.cn/g' /etc/apk/repositories
RUN apk add git

ARG GOPROXY
ARG ALL_PROXY

RUN git clone --progress https://github.com/juanfont/headscale
RUN cd headscale && VERSION="$(git describe --always --tags --dirty)" && \
	LD_FLAGS="-s -w -X github.com/juanfont/headscale/cmd/headscale/cli.Version=$VERSION" && \
	CGO_ENABLED=0 GOOS=linux go install -tags ts2019 -ldflags="$LD_FLAGS" -a ./cmd/headscale


ARG BASE_ALPINE
FROM $BASE_ALPINE

RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.ustc.edu.cn/g' /etc/apk/repositories
RUN apk add --no-cache iproute2

COPY --from=builder /go/bin/headscale /bin/

RUN ln -s /var/lib/headscale/etc /etc/headscale && mkdir -p /run/headscale \
		/var/lib/headscale/etc /var/lib/headscale/cert /var/lib/headscale/data

CMD ["headscale", "serve"]
