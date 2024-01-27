---
layout: default
title: Beginner's Docker Guide on Synology
---

# Beginner's Docker Guide on Synology

!!! note
    In case you have any difficulties following this guide or have found some errors, please leave a note on the [forums](https://forums.slimdevices.com/forum/developer-forums/developers/1668265-documentation-update-call-for-volunteers). Thanks!

If you have a Synology NAS which can run Docker containers, you are in luck! Check [here](https://www.synology.com/nl-nl/dsm/packages/ContainerManager) to check if your Synology NAS is able to run Docker containers.

## Assumptions

In this guide the following assumptions apply:

- If you already have the Synology LMS packaged installed, uninstall it first. This will free up the LMS network ports so the container can use them.
- This docker container runs as your user. You can also create a specific user for running the container, in that case replace the UID/PUID with the correct identifier. 
- Your music is stored in the shared folder `music` on the first volume (so the path to your music collection is `/volume1/music`).
- (Optional) you have a shared folder called playlist on the first volume with path `/volume1/playlist`.
- Your user has read-only or read-write access to the music folder. If you also have a playlist folder, your user needs read-write access to this folder.
- The state of the docker image is saved in the folder `/docker/logitechmediaserver`. The path can be anything, but it is advisable to restrict write access for other users to this folder. 

## Find out the UID and GID of your user

The [UID](https://en.wikipedia.org/wiki/User_identifier) and [GID](https://en.wikipedia.org/wiki/Group_identifier) values for the default user on Synlogy NAS are usually 1026 and 100. There is a very simple way to check the values as follows\:

1. Create a new Scheduled task as an User-defined script
2. Name the script whatever you see fit
3. Set it to be not repeating
4. Set it to send run details to your email
5. Write the script\:

    ```
    id
    ```

6. Run it and soon enough, you will receive an email containing the UID and GID values.

## Download the Docker image

1. In the Synology GUI start up the app Container Manager.
2. Go to Register.
3. Search for `logitechmediaserver` and download the `lmscommunity/logitechmediaserver` image.
4. Select the `stable` tag. The image will now download.

## Configure the Docker container

Now the correct image has been downloaded, it is time to start and configure the container.

1. In the "Container Manager" app, go to "Image", select the `lmscommunity/logitechmediaserver` image and press "Run".
2. Choose a suitable name, e.g. `logitechmediaserver`, and press "Next".
3. In the "Port Settings" section, add the following ports :

    | Local port | Container port | Protocol |
    | --- | --- | --- |
    | 9000 | 9000 | tcp |
    | 9090 | 9090 | tcp |
    | 3483 | 3483 | tcp |
    | 3483 | 3483 | udp |

4. In the "Volume Settings" section, add the following volumes:

    | Local folder | Container folder | Mode |
    | --- | --- | --- |
    | /docker/logitechmediaserver | /config | rw |
    | /volume1/music | /music | ro |
    | /volume1/playlist | /playlist | rw |

5. Add the UID and GID from the step above, and the correct TZ ([timezone](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)) to the "Enviroment" section:

    | Variable | Value |
    | --- | --- |
    | PUID | 1026 (for example, see the [UID/GID step above](#find-out-the-uid-and-gid-of-your-user)) |
    | PGID | 100 (for example, see the [UID/GID step above](#find-out-the-uid-and-gid-of-your-user)) |
    | TZ | Europe/Zurich |

6. Select "Next" and "Done". The container will atomatically start. Congrats, you're done!