# OP-TEE + fTPM using FVP
This git containst repo manifests to be able to clone all source code needed to
be able to setup a full OP-TEE developer build which also enables Microsofts
fTPM implementation.

Note that we this setup contains a couple of different forks (fTPM, Linux
kernel, ibmtpm20tss, build). Likewise some steps will be done after booting up the
system.

### Get the source
Pre-condition, install the pre-reqs mentioned at the official OP-TEE docs:
https://optee.readthedocs.io/en/latest/building/prerequisites.html#prerequisites

When done, continue here.

```bash
$ mkdir optee-ftpm && cd optee-ftpm
$ repo init -u https://github.com/javieralso-arm/manifest.git -b fvp-tpm -m fvp_tpm.xml 
$ repo sync -j4 --no-clone-bundle
```

Next we need to get WolfSSL
```bash
$ cd <optee-ftpm>/arm-ms-tpm-20-ref
$ git submodule init
$ git submodule update
```

Obtain the Armv8-A Foundation Platform (for Linux Hosts only) as mentioned on the
officienal OP-TEE docs:
https://optee.readthedocs.io/en/latest/building/devices/fvp.html#build-instructions
For this, you will need to log into the ARM Self Service.

### Compile
The initial build takes quite some time, have patience!
```bash
$ cd <optee-ftpm>/build
$ make toolchains -j2
$ make -j`nproc`
```

### Run
```bash
$ cd <optee-ftpm>/build
$ make -j`nproc` run
```

After a while, you should see FVP running and two shells, one for the secure world and nother
one for the norma one.

#### FVP Shell

- Login with the user `root`. It does not require a password.
- Run command `ftpm` which will start the fTPM service and will show PCR0:

```bash
buildroot login: root
# ftpm
[ 4374.055020] random: crng init done
count 1 pcrUpdateCounter 30 
 digest length 32
 0e 31 62 df 0e ba 78 85 a3 ac 7c 50 f6 ea 21 5d 
 f6 35 bd 3e f8 da 85 21 fd ad 09 ad 9a 15 9e 31 
#
```
The command will take few seconds the first time, so please be patient.

### Available commands

The `ftpm` command is an alias that executes two other alias:
- `ftpm_mod`: Alias for `insmod /tpm_ftpm_tee.ko`, to load the ftpm service.
- `ftpm_getpcr`: Alias for `pcrread -ha 0`, which reads the PCR0 (used by TF-A
Measured Boot to write its measurements).

#### To re-run without re-compile
```bash
$ cd <optee-ftpm>/build
$ make run-only
```

# Official OP-TEE documentation
All other official OP-TEE documentation has moved to
http://optee.readthedocs.io. The information that used to be here in this git
can be found under [manifest].

// OP-TEE core maintainers

[manifest]: https://optee.readthedocs.io/building/gits/manifest.html
