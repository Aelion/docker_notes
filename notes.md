# add a repository name to the created image
docker image build -t my-image .

# run docker image
docker run --rm e30ffc9cc8e5

# run docker image with argument and image name
 docker run --rm my-image --whatever

##This image is awesome!
FROM ubuntu
COPY . /app
RUN apt -y update && apt -y install curl
ENTRYPOINT [ "/app/app.sh", "--argument" ]

root@kubmaster:~/linkedin/04_03_before# docker run --rm my-image --whatever
============================================================

     🎉🎉🎉🎉🎉 WELCOME TO THE DATE APP!!! 🎉🎉🎉🎉🎉

============================================================
INFO: Fetching date from the Internet. Hang on!


🕰️  The date is: Thu Dec 14 15:02:00 UTC 2023

⚙️  Here's what's running in your container:

UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  6 15:01 ?        00:00:00 bash /app/app.sh --argument --whatever
root        14     1  0 15:02 ?        00:00:00 ps -ef


We also got some arguments: --argument



We also got some arguments: --whatever



⚙️  2 arguments were provided to this application.


