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

