Distribute
==========

Run your parser on multiple machines in parallel. Distribute provisions machines
on ec-2 or azure, then processes chunks of your data on each machine, and
finally terminates the machines.

Before you begin, follow the instructions in [Setup](#setup) to install Distribute.


1.  Launch instances on ec-2 or azure.

    ```bash
    fab launch:cloud='ec-2',num=1
    ```
    This will launch 1 instance on ec-2. It will also put status information
    about the launched instance into `.state`.

2.  Install dependencies on remote machines
    ```bash
    fab install
    ```

3.  Copy chunks to remote machines
    ```bash
    fab copy
    ```
    You can provide additional parameters to override defaults:

    ```bash
    fab copy:input=xml_parsed.json,batch_size=100    
    ```

4.  Run parser on remote machines
    ```bash
    fab parse
    ``` 

5.  Collect results
    ```bash
    fab collect
    ```

6.  Terminate remote machines
    ```bash
    fab terminate
    ```
    If termination is successful, the status information in `.state` will be deleted.

Your parsed information should now be available as a tsv file in your working directory.

## Setup

### Dependencies

If you have sudo rights, run `./setup.sh`.

If you don't have sudo rights, follow these steps instead. These have been tested on raiders3 (Stanford):
```
cd bazaar
wget https://raw.githubusercontent.com/pypa/virtualenv/develop/virtualenv.py
python virtualenv.py env --no-setuptools
source env/bin/activate
wget https://raw.github.com/pypa/pip/master/contrib/get-pip.py
python get-pip.py
pip install fabric
```

The fab command line tool should work now.
Note that you will have to run `source env/bin/activate` after each login to initialize the environment.

### Generate SSH Keys 

Now, generate SSH keys with
```
./generate-keys.sh
```

### Build

Finally, create a self-extracting installer that will be run on worker nodes:
```
cd installer
./build 
cd ..
```

### Set EC2 or Azure credentials

Edit `env\_local.sh` as needed.

For ec2, we recommend storing credentials at `~/.aws/credentials` following Amazon's recommendations.

For azure, upload `ssh/mycert.cer` to the management portal via the "Upload" action of the "Settings" tab, and set the following variable in `env\_local.sh`:
```
AZURE_SUBSCRIPTION_ID='...'
```

For each, ec2 and azure, you can set the instance types you would like to use in `env\_local.sh`.

Initialize by running
```
source env_local.sh
````
