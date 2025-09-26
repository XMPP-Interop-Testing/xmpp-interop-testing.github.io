---
layout: post
title:  "Containers!"
cover-img: /assets/img/container_splash.jpg
date:   2024-09-04 17:33:07 +0100
author: Dan Caseley
categories:
---

I know it's not universal, but I _love_ working with containers. Once you've got your container runtime, it doesn't matter what you're running, whether it's a web app, a database, a command line tool, or something else entirely. It doesn't matter whether it was written in Rust or Erlang or Perl or Java or something properly esoteric. It just works, all at the cost of a few extra megabytes for the wrapper (or the container, if you will).

For our project, we opted early on to try something a little different. Rather than choose between the most popular engine and toolchain (Docker) and something more fully open-source source, we opted to do both. We wanted Docker and OCI images, and we wanted Docker and open-source build tools, to prove it can be built and it can be run by whoever wants to, however they want to.

I don't know how many folks have been down the rabbit-hole of "what's the difference between the Docker image format and OCI?" - the differences between the image formats are really fairly miniscule. Right now at least. Docker donated their tech of the day to form the initial OCI standards, and most tooling out there supports both Docker and OCI building and running. But that might not always be the case.

What did we do?

Firstly, for build instructions, we created a Dockerfile and a Containerfile. Sure, podman will consume a Dockerfile and produce an OCI image, but given the above mention of possible future divergence, it made sense. We also liked the idea that it gave the appropriate credit to the FOSS community - that whilst all Docker images are container images, not all container images are Docker images, and calling them Docker images devalues the gargantuan effort put into open [tooling](https://github.com/containers/) and [standards](https://github.com/opencontainers/).

Next, we tested that our work could actually be used to make a working container.

Here's how it looks OCI:

```
~/git/smack-sint-server-extensions> podman build . -t sintse-oci:test
[1/3] STEP 1/3: FROM docker.io/library/eclipse-temurin:17-jdk-noble AS pom
[1/3] STEP 2/3: WORKDIR /usr/src
<snip>
[3/3] STEP 6/6: ENTRYPOINT ["/sbin/entrypoint.sh"]
[3/3] COMMIT sintse-oci:test
--> 56d0a8a75584
Successfully tagged localhost/sintse-oci:test
56d0a8a75584c3c1cc41d535dac50c244a0acf04bb3cf4abca989363d46a1608


~/git/smack-sint-server-extensions> podman inspect sintse-oci:test
<snip>
          "Annotations": {
               "org.opencontainers.image.base.digest": "sha256:5579258e20135a9b54c16b9038008adcff50bd1d637824ed564d5b1cc881cba8",
               "org.opencontainers.image.base.name": "docker.io/library/eclipse-temurin:17-jre-noble"
          },
          "ManifestType": "application/vnd.oci.image.manifest.v1+json",
<snip>


~/git/smack-sint-server-extensions> podman run --network host sintse-oci:test --adminAccountUsername admin --adminAccountPassword admin
Saving JUnit-compatible XML file with results to /logs/test-results.xml
Saving debug logs in /logs
<snip>
Test run (id: bjvrd) finished! 266 tests were successful (✔), 2 failed (💀), and 29 were impossible to run (✖).
<snip>
```

And this is Docker:

```
~/git/smack-sint-server-extensions> docker build . -t sintse-docker:test
[+] Building 96.5s (21/21) FINISHED                                                                                             docker:default
 => [internal] load build definition from Dockerfile                                                                                      0.0s
 => => transferring dockerfile: 860B                                                                                                      0.0s
 => [internal] load metadata for docker.io/library/eclipse-temurin:17-jre-noble 
 <snip>
  => exporting to image                                                                                                                    0.1s
 => => exporting layers                                                                                                                   0.1s
 => => writing image sha256:922ebdbac4d3c45b00c54ac2c5687b73312bd8697941a25380e7870708a5b25f                                              0.0s
 => => naming to docker.io/library/sintse-docker:test  


~/git/smack-sint-server-extensions> docker inspect sintse-docker:test
<snip>
        "Comment": "buildkit.dockerfile.v0",
<snip>


~/git/smack-sint-server-extensions> docker run --network host sintse-docker:test --adminAccountUsername admin --adminAccountPassword admin
Saving JUnit-compatible XML file with results to /logs/test-results.xml
Saving debug logs in /logs
<snip>
Test run (id: 4z7ev) finished! 266 tests were successful (✔), 2 failed (💀), and 29 were impossible to run (✖).
<snip>
```

We added CI to run a podman and Docker build on every PR to the repo, just to be sure contributors would be warned if they did something to break it, or in case a podman itself got an upgrade that caused a breakage. We don't need to run the tests - we've got other checks around to check the validity and the capability of test running - here we're more relying on the output of the `podman build` command to prove that we can build a container image.

Next, we added CI to the main branch to build and push a "bleeding edge" image to a container registry. We chose GitHub because (a) it's with our code (b) it has OCI support. The same CI also deals with publishing tagged images when the repo is tagged.

It's exciting! With containerised servers and containerised tests, there's far less onus on developers to manage dependencies or be dependent on operating systems or versions. Not only does this reduce the barrier to entry for folks wanting to adopt the tests, it's also far easier for me!

_Splash image courtesy of [CHUTTERSNAP, Unsplash](https://unsplash.com/photos/birds-photo-of-cityscape-9cCeS9Sg6nU?utm_content=creditShareLink&utm_medium=referral&utm_source=unsplash)_
