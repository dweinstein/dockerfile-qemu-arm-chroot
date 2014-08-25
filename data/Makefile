.PHONY : all build run clean
all: build

build:
	docker build -t qemu-arm-data .
run: clean
	-docker run --name qemu-arm_data qemu-arm-data &> /dev/null
clean:
	-docker stop qemu-arm_data &> /dev/null
	-docker rm qemu-arm_data &> /dev/null
