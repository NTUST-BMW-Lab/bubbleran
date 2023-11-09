# Kitware APT Repository

1. If you are using a minimal Ubuntu image or a Docker image, you may need to install the following packages:

    ```bash
    sudo apt-get update
    sudo apt-get install ca-certificates gpg wget
    ```

2. Obtain a copy of our signing key:

    ```bash
    wget -O - https://apt.kitware.com/keys/kitware-archive-latest.asc 2>/dev/null | gpg --dearmor - | sudo tee /usr/share/keyrings/kitware-archive-keyring.gpg >/dev/null
    ```

3. Add the repository to your sources list and update.
    - For Ubuntu Jammy Jellyfish (22.04):

        ```bash
        echo 'deb [signed-by=/usr/share/keyrings/kitware-archive-keyring.gpg] https://apt.kitware.com/ubuntu/ jammy main' | sudo tee /etc/apt/sources.list.d/kitware.list >/dev/null
        sudo apt-get update
        ```

    - For Ubuntu Focal Fossa (20.04):

        ```bash
        echo 'deb [signed-by=/usr/share/keyrings/kitware-archive-keyring.gpg] https://apt.kitware.com/ubuntu/ focal main' | sudo tee /etc/apt/sources.list.d/kitware.list >/dev/null
        sudo apt-get update
        ```
4. Install the kitware-archive-keyring package to ensure that your keyring stays up to date as we rotate our keys:
    ```bash
    sudo rm /usr/share/keyrings/kitware-archive-keyring.gpg
    sudo apt-get install kitware-archive-keyring
    ```

5. As an optional step, if you would like to subscribe to release candidates in addition to production releases, you can add our release candidate repository to your sources.

   - For Ubuntu Jammy Jellyfish (22.04):
        ```bash
        echo 'deb [signed-by=/usr/share/keyrings/kitware-archive-keyring.gpg] https://apt.kitware.com/ubuntu/ jammy-rc main' | sudo tee -a /etc/apt/sources.list.d/kitware.list >/dev/null

        sudo apt-get update
        ```

    - For Ubuntu Focal Fossa (20.04):
        ```bash
        echo 'deb [signed-by=/usr/share/keyrings/kitware-archive-keyring.gpg] https://apt.kitware.com/ubuntu/ focal-rc main' | sudo tee -a /etc/apt/sources.list.d/kitware.list >/dev/null
        sudo apt-get update
        ```

    Note that if you add the release candidate repository, you will still need to add the main repository as well, as the release candidate repository does not provide production releases on its own.

Now you can install any package from our APT repository. As an example, try installing the cmake package:

```bash
sudo apt-get install cmake


```

