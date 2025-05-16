    "msg": "Using a SSH password instead of a key is not possible because Host Key checking is enabled and sshpass does not support this.  Please add this host's fingerprint to your known_hosts file to manage this host."

    how to file ssh in the host

    ```diff
    #file  ./ansible.cfg
    [defaults] 
    host_key_checking = False 
    ```
    or 
    ```diff
export ANSIBLE_HOST_KEY_CHECKING=False
    ```
    
    
    ```