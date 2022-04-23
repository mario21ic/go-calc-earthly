VERSION 0.6

FROM golang:1.17-alpine
RUN apk --update --no-cache add git
WORKDIR /app

all:
    BUILD +lint
    BUILD +tests
    BUILD +build
    #BUILD +docker

deps:
    COPY go.mod go.sum ./
    COPY lib ./lib
    RUN go mod download
    # Output these back in case go mod download changes them
    SAVE ARTIFACT go.mod AS LOCAL go.mod
    SAVE ARTIFACT go.sum AS LOCAL go.sum

lint:
    FROM +deps
    COPY main.go ./
    RUN go vet ./

tests:
    FROM +deps
    COPY ./* ./
    RUN go test -v lib/math_test.go -coverpkg=./...

build:
    FROM +deps
    ARG version
    RUN echo $version > version.txt

    #ENV GOOS=darwin
    #ENV GOARCH=arm64

    COPY main.go ./
    RUN go build -o build/go-example-$version main.go
    RUN CGO_ENABLED=0 go build \
        -installsuffix 'static' \
        -o ./build/go-calc-$version main.go

    SAVE ARTIFACT build/go-calc-$version /go-calc AS LOCAL build/go-calc
    SAVE ARTIFACT version.txt AS LOCAL build/version.txt

docker:
    FROM scratch # Sin base
    ARG tag='latest'
    BUILD +build # save file
    COPY +build/go-calc /go-example/go-calc
    ENTRYPOINT ["/go-example/go-calc"]
    SAVE IMAGE mario21ic/go-calc:$tag
    #SAVE IMAGE --push mario21ic/go-calc:$tag

# Example of docker in docker
test-docker:
    FROM earthly/dind:alpine
    WITH DOCKER --load mario21ic/go-calc:latest=+docker
        RUN docker run mario21ic/go-calc:latest
    END

