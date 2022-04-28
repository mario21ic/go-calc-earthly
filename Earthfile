VERSION 0.6

# Base
FROM golang:1.17-alpine
# No requiere make ni bash
RUN apk --update --no-cache add git
WORKDIR /app
# Nota: cada target se ejecuta en un container

all:
    BUILD +lint
    BUILD +tests
    BUILD +build
    #BUILD +docker

deps:
    COPY go.mod go.sum ./
    COPY lib ./lib
    RUN go mod download
    # Grabar los archivos en local
    SAVE ARTIFACT go.mod AS LOCAL go.mod
    SAVE ARTIFACT go.sum AS LOCAL go.sum

lint:
    FROM +deps # incluir los steps
    COPY main.go ./
    RUN go vet ./

tests:
    FROM +deps
    COPY ./* ./
    RUN go test -v lib/math_test.go -coverpkg=./...


build:
    FROM +deps # incluir los steps de deps
    ARG version
    RUN echo $version > version.txt # escribir la version en un archivo

    # Mac m1
    #ENV GOOS=darwin
    #ENV GOARCH=arm64

    COPY main.go ./
    # Compilar usando la variable version
    RUN CGO_ENABLED=0 go build \
        -installsuffix 'static' \
        -o ./build/go-calc-$version main.go

    # Grabar el binario en el container como /go-calc y a la vez en local
    SAVE ARTIFACT build/go-calc-$version /go-calc AS LOCAL build/go-calc

    # Grabar el archivo version.txt en local para test de la var version
    SAVE ARTIFACT version.txt AS LOCAL build/version.txt


docker:
    FROM scratch # Sin base, con su propio image
    ARG tag='latest'
    BUILD +build # ejecutar el target build
    # Copiamos /go-calc desde el target build
    COPY +build/go-calc /go-example/go-calc
    ENTRYPOINT ["/go-example/go-calc"]
    #SAVE IMAGE mario21ic/go-calc-earthly:$tag
    SAVE IMAGE --push mario21ic-earthly/go-calc:$tag # para enviar al docker hub

# Example of docker in docker
test-docker:
    FROM earthly/dind:alpine
    WITH DOCKER --load mario21ic/go-calc-earthly:latest=+docker
        RUN docker run mario21ic/go-calc-earthly:latest
    END

