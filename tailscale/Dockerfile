ARG BASE_GOLANG
FROM $BASE_GOLANG AS builder

WORKDIR /go/src/
ENV GOPATH /go

RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.ustc.edu.cn/g' /etc/apk/repositories
RUN apk add git

ARG GOPROXY
ARG ALL_PROXY

RUN git clone --progress https://github.com/tailscale/tailscale
RUN cd tailscale && \
	LD_FLAGS="-s -w -X tailscale.com/version.Long=$(git describe --always) \
		-X tailscale.com/version.Short=$(git describe --always|cut -d- -f1) \
		-X tailscale.com/version.GitCommit=$(git rev-parse --short HEAD)" && \
	GOFLAGS="-buildmode=pie -mod=readonly -modcacherw" \
	CGO_ENABLED=0 GOOS=linux go install -ldflags="$LD_FLAGS" -v \
		./cmd/tailscale ./cmd/tailscaled ./cmd/containerboot


ARG BASE_ALPINE
FROM $BASE_ALPINE

RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.ustc.edu.cn/g' /etc/apk/repositories
RUN apk add --no-cache iproute2

COPY --from=builder /go/bin/* /bin/

# use the official wrapper
CMD ["containerboot"]
