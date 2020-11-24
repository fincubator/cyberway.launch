FROM golang:1.14 as builder

COPY . /opt/app

WORKDIR /opt/app 

RUN go build

FROM debian:10

COPY --from=builder /opt/app/stihi-backend /usr/local/bin/
RUN chmod +x /usr/local/bin/stihi-backend

CMD ["stihi-backend"]