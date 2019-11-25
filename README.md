Role Name: Librespot
=========

Installation and configuration of Librespot client on arm64 and armhf based systems that support systemd. This role also
includes the configuration of a [Hifiberry](https://www.hifiberry.com/shop/boards/hifiberry-dac-rtc/) device. If you
like any feature is missing, dont hesitate to create a ticket but also feel free to create a PR to add it yourself.

Requirements
------------

Only for the ```librespot-java``` type of installation:
```
- src: geerlingguy.java
  version: 1.9.6
  name: java
  when: librespot_installation_type == "librespot-java"
```

Role Variables
--------------
Variables to define type of installation:
```
librespot_installation_type: librespot # type of installation of librespot: librespot(rust), librespot-java, raspotify
raspotify_enable_hifiberry: "false" # boolean, if you got a hifiberry device
```
Variables for the ```raspotify``` type of installation:
```
raspotify_device_name: "{{ ansible_hostname }}" # device name on Spotify Connect
raspotify_bitrate: "320" # bitrate, one of 96 (low quality), 160 (default quality), or 320 (high quality)
raspotify_options: "--zeroconf-port 40995" # additional command line arguments for librespot can be set below.
raspotify_cache_args: "" # use /var/cache/librespot for cache location if needed so. Cache is disabled by default
raspotify_volume: "--enable-volume-normalisation --linear-volume --initial-volume=100" # default volume when connecting to the client
raspotify_args: "--backend alsa --device default" # backend could be set to pipe here, but it's for very advanced use cases of
raspotify_git_branch: "master" # git branch/commit from which you want to build it from
raspotify_librespot_git_branch: "dev" # git branch from librespot that you want to build it from, leave empty if you want to use default one
```
Variables for the native ```librespot``` type of installation:
```
librespot_device_name: "{{ ansible_hostname }}" # device name on Spotify Connect
librespot_bitrate: "320" # bitrate, one of 96 (low quality), 160 (default quality), or 320 (high quality)
librespot_options: "--zeroconf-port 40995" # additional command line arguments for librespot can be set below
librespot_cache_args: "--disable-audio-cache" # use /var/cache/librespot for cache location if needed so. Cache is disabled by default
librespot_volume: "--enable-volume-normalisation --linear-volume --initial-volume=100" # default volume when connecting to the client
librespot_args: "--backend alsa" # backend could be set to pipe here, but it's for very advanced use cases of
librespot_git_branch: "dev" # git branch/commit from which you want to build it from
```
Variables for the native ```librespot-java``` type of installation:
```
librespot_java_device_name: "{{ ansible_hostname }}" # device name on Spotify Connect
librespot_java_device: "Computer" # device type
librespot_java_auth_strategy: "ZEROCONF" # authentication strategy
librespot_java_auth_username: "" # optional username
librespot_java_auth_password: "" # optional password
librespot_java_auth_blob: "" # optional blob
librespot_java_zeroconf_port: 40995 # dedicated port running, "-1" for random port
librespot_java_zeroconf_listen_all: "true" # boolen for wildcard interfaces
librespot_java_zeroconf_interfaces: "" # define interfaces
librespot_java_cache_enabled: "true" # enable cacheing
librespot_java_cache_dir: "./cache" # directory for caching
librespot_java_cache_do_clean_up: "true" # periodical clean up of cache
librespot_java_preload_enabled: "true" # preload songs
librespot_java_player_autoplay_enabled: "true" # enable autoplay feature
librespot_java_player_preferred_audio_quality: "VORBIS_320" # bitrate, one of 96 (low quality), 160 (default quality), or 320 (high quality)
librespot_java_player_normalisation_enable: "true" # normalisation for songs
librespot_java_player_normalisation_pregain: 0.0 # rate of the normalisation
librespot_java_player_initial_volume: 65536 # inital volume when connecting to the client
librespot_java_player_log_available_mixers: "false" # list all available mixers
librespot_java_player_mixer_search_keywords: "plughw" # keywords to pick a mixer
librespot_java_player_enable_loading_state: "true" # enable loading state
librespot_java_playertracks_usecdn: "true" # use cdn for playertracks
librespot_java_player_episodes_usecdn: "true" # use cdn for episodes
librespot_java_git_branch: "master" # git branch/commit from which you want to build it from
```

Dependencies
------------

The roles will build each of the applications from the sources on your source machine before pushing the artifact to 
your target system. Therefore it is required that your source machine matches all the required dependencies for building
the projects.

Depending of the type of installation take a look at the following:
* [Librespot Dependencies](https://github.com/librespot-org/librespot#building)
* [Librespot-Java Dependencies](https://github.com/librespot-org/librespot-java#build-it)
* [Raspotify Dependencies](https://github.com/dtcooper/raspotify#building-the-package-yourself)

Example Playbook
----------------

    - hosts: all
      roles:
        - role: xmordax.librespot

Ansible Guide
-------------
As request here a quick guide for Ansible to get this project running.

1. [Install Ansible on your local machine.](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)
2. Create a empty working directory.
3. Change directory into your newly created directory and execute the following command: ```ansible-galaxy install xmordax.librespot -p ./roles```.
This should have created a folder called roles which includes the code from this repository
4. Create two new files in the root of your working directory: ```playbook.yml``` and ```inventory```.
5. In the ```inventory``` file you need to write the configuration on how to connect to your target system, in my
example I will use SSH. If you want to use any other kind of authentication, please check Ansible Documentation on how
to configure your inventory file.
    ```
    [server]
    hostname ansible_host=<IP_OF_TARGET_MACHINE> ansible_user=<USER_ON_TARGET_MACHINE> ansible_ssh_private_key_file=<PATH_TO_SSH_KEY>
    ``` 
6. The ```playbook.yml``` file is used for configuring your deployment, in my case I will do just basic configuration to
keep this guide simple.
    ```
    - hosts: server
      become: true
      roles:
        - librespot
    
      vars:
        # Raspotify configuration
        librespot_installation_type: "librespot"
        raspotify_device_name: "home-spotify-connect"
    ```
7. Deploy with the following command: ```ansible-playbook playbook.yml -i inventory```.

#### Important
Make sure to first read the [dependencies](https://github.com/xMordax/ansible-role-librespot#dependencies) section of
this project. If you will ever have any issues with the deployment, please first refer to the official [ Ansible Documentation](https://docs.ansible.com/ansible/latest/index.html)
and try solving the problems by yourself before creating a ticket.

License
-------

MIT / BSD

Author Information
------------------

This role was created in 2019 by [Aljaz Gantar](https://github.com/xMordax).
