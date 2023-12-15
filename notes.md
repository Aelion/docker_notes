# add a repository name to the created image
docker image build -t my-image .

# run docker image
docker run --rm e30ffc9cc8e5

# run docker image with argument and image name
 docker run --rm my-image --whatever

# DIFFERENCE BETWEEN ENTRYPOINT AND CMD (creds to stackoverflow)
Docker has a default entrypoint which is /bin/sh -c but does not have a default command.

When you run docker like this: docker run -i -t ubuntu bash the entrypoint is the default /bin/sh -c, the image is ubuntu and the command is bash.

The command is run via the entrypoint. i.e., the actual thing that gets executed is /bin/sh -c bash. This allowed Docker to implement RUN quickly by relying on the shell's parser.

Later on, people asked to be able to customize this, so ENTRYPOINT and --entrypoint were introduced.

Everything after the image name, ubuntu in the example above, is the command and is passed to the entrypoint. When using the CMD instruction, it is exactly as if you were executing
docker run -i -t ubuntu <cmd>
The parameter of the entrypoint is <cmd>.

You will also get the same result if you instead type this command docker run -i -t ubuntu: a bash shell will start in the container because in the ubuntu Dockerfile a default CMD is specified:
CMD ["bash"].

As everything is passed to the entrypoint, you can have a very nice behavior from your images. @Jiri example is good, it shows how to use an image as a "binary". When using ["/bin/cat"] as entrypoint and then doing docker run img /etc/passwd, you get it, /etc/passwd is the command and is passed to the entrypoint so the end result execution is simply /bin/cat /etc/passwd.

Another example would be to have any cli as entrypoint. For instance, if you have a redis image, instead of running docker run redisimg redis -H something -u toto get key, you can simply have ENTRYPOINT ["redis", "-H", "something", "-u", "toto"] and then run like this for the same result: docker run redisimg get key.

The ENTRYPOINT specifies a command that will always be executed when the container starts.

The CMD specifies arguments that will be fed to the ENTRYPOINT.

If you want to make an image dedicated to a specific command you will use ENTRYPOINT ["/path/dedicated_command"]

Otherwise, if you want to make an image for general purpose, you can leave ENTRYPOINT unspecified and use CMD ["/path/dedicated_command"] as you will be able to override the setting by supplying arguments to docker run.

For example, if your Dockerfile is:

FROM debian:wheezy  

ENTRYPOINT ["/bin/ping"]
CMD ["localhost"]
Running the image without any argument will ping the localhost:

$ docker run -it test
PING localhost (127.0.0.1): 48 data bytes
56 bytes from 127.0.0.1: icmp_seq=0 ttl=64 time=0.096 ms  

56 bytes from 127.0.0.1: icmp_seq=1 ttl=64 time=0.088 ms
56 bytes from 127.0.0.1: icmp_seq=2 ttl=64 time=0.088 ms
^C--- localhost ping statistics ---
3 packets transmitted, 3 packets received, 0% packet loss
round-trip min/avg/max/stddev = 0.088/0.091/0.096/0.000 ms
Now, running the image with an argument will ping the argument:

$ docker run -it test google.com
PING google.com (173.194.45.70): 48 data bytes
56 bytes from 173.194.45.70: icmp_seq=0 ttl=55 time=32.583 ms
56 bytes from 173.194.45.70: icmp_seq=2 ttl=55 time=30.327 ms
56 bytes from 173.194.45.70: icmp_seq=4 ttl=55 time=46.379 ms

^C--- google.com ping statistics ---
5 packets transmitted, 3 packets received, 40% packet loss
round-trip min/avg/max/stddev = 30.327/36.430/46.379/7.095 ms
For comparison, if your Dockerfile is:

FROM debian:wheezy
CMD ["/bin/ping", "localhost"]
Running the image without any argument will ping the localhost:

$ docker run -it test
PING localhost (127.0.0.1): 48 data bytes
56 bytes from 127.0.0.1: icmp_seq=0 ttl=64 time=0.076 ms
56 bytes from 127.0.0.1: icmp_seq=1 ttl=64 time=0.087 ms
56 bytes from 127.0.0.1: icmp_seq=2 ttl=64 time=0.090 ms
^C--- localhost ping statistics ---
3 packets transmitted, 3 packets received, 0% packet loss
round-trip min/avg/max/stddev = 0.076/0.084/0.090/0.000 ms
But running the image with an argument will run the argument:

docker run -it test bash
root@e8bb7249b843:/#
See this article from Brian DeHamer for even more details: https://www.ctl.io/developers/blog/post/dockerfile-entrypoint-vs-cmd/


