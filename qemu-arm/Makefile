.PHONY : all build run clean
all: build

build:
	docker build -t qemu-arm .
clean:
	-docker stop qemu-arm &> /dev/null
	-docker rm qemu-arm &> /dev/null
