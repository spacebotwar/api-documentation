# SpaceBotWar API documentation

This is the documentation for the SpaceBotWar server API.

Documentation is run from a Jekyll implementation in a Docker container which should
make it much easier to install.

# install

First build the docker container.

{% highlight bash %}
  $ ./build_docker.sh
{% endhighlight %}

Then run a shell in the container.

{% highlight bash %}
$ ./run_docker.sh
  root@17d2ed3096e5:/docs# jekyll serve
{% endhighlight %}



