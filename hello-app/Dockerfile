FROM golang:alpine as build

WORKDIR /go

COPY . .

RUN go build -o hello-app . 

FROM alpine

COPY --from=build /go/hello-app /bin/hello-app

CMD ["hello-app"]
