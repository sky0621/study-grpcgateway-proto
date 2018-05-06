# study-grpcgateway-proto

## env

ubuntu 17.10 (64bit)

## ref

https://github.com/grpc-ecosystem/grpc-gateway

## pre

sudo apt-get install -y autoconf
sudo apt-get install -y dh-autoreconf

### for swagger

sudo ldconfig
export LD_LIBRARY_PATH=/usr/local/lib

## setup

### install protobuf

mkdir tmp
cd tmp
git clone https://github.com/google/protobuf
cd protobuf
./autogen.sh
./configure
make
make check
sudo make install

### go get

go get -u github.com/grpc-ecosystem/grpc-gateway/protoc-gen-grpc-gateway
go get -u github.com/grpc-ecosystem/grpc-gateway/protoc-gen-swagger
go get -u github.com/golang/protobuf/protoc-gen-go

## generate

### gRPC stub

protoc -I/usr/local/include -I. \
  -I$GOPATH/src \
  -I$GOPATH/src/github.com/grpc-ecosystem/grpc-gateway/third_party/googleapis \
  --go_out=plugins=grpc:gen \
  *.proto

### reverse-proxy

protoc -I/usr/local/include -I. \
  -I$GOPATH/src \
  -I$GOPATH/src/github.com/grpc-ecosystem/grpc-gateway/third_party/googleapis \
  --grpc-gateway_out=logtostderr=true:gen \
  *.proto

### swagger

protoc -I/usr/local/include -I. \
  -I$GOPATH/src \
  -I$GOPATH/src/github.com/grpc-ecosystem/grpc-gateway/third_party/googleapis \
  --swagger_out=logtostderr=true:gen \
  *.proto

## curl

curl -X GET --header "Accept: application/json" "http://localhost:8080/v1/sample/echo"
{"error":"all SubConns are in TransientFailure, latest connection error: connection error: desc = \"transport: Error while dialing dial tcp 127.0.0.1:9090: getsockopt: connection refused\"","code":14}
