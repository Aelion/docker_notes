# add a repository name to the created image
docker image build -t my-image .

# run docker image
docker run --rm e30ffc9cc8e5

# run docker image with argument and image name
 docker run --rm my-image --whatever



