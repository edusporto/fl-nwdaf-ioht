# fl-nwdaf-ioht

Reproducible experiments integrating Federated Learning (FL), the 5G Core Network Data Analytics Function (NWDAF), and the Internet of Health Things (IoHT).

The `docker-compose.yaml` file orchestrates the experiments via Docker Compose. It is based on the [free5gc](https://github.com/free5gc/free5gc-compose) project and starts a custom NWDAF based on [oliveiraleo/mnc_NWDAF](https://github.com/oliveiraleo/mnc_NWDAF) that performs federated learning on either [WUSTL EHMS 2020](https://www.cse.wustl.edu/~jain/ehms/index.html), [ECU-IoHT](https://github.com/CSCRC-SCREED/ECU-IoHT), or [MedMNIST](https://medmnist.com/), as discussed on the [edusporto/fl-wsl](https://github.com/edusporto/fl-wsl) repository.

## Prerequisites

Running these experiments requires installing the Docker Enginer, Docker Compose v2, and the GTP5G kernel module. See the [free5gc/free5gc-compose](https://github.com/free5gc/free5gc-compose) repository for more details.

A couple of git submodules are referenced by this repository. Add them to your copy with:

```
git submodule init
git submodule update
```

## Running

You may run the entire 5G Core with `docker compose up`, but that is not necessarily needed to run the experiments related to the NWDAF. First, run the NWDAF:

```
docker compose up nwdaf
```

Now, run the training manager that hooks to the federated learning module. This will start four services: two federated learning clients `client-0` and `client-1`, a link named `superlink` that connects them to the central managing server, and the training server. This may take a long while and use a few gigabytes of storage depending on your hardware.

```
docker compose up training-manager
```

If this command does not print feedback during building, you may try to manually build the FL service and try again:

```
cd fl_ioht/fl-wsl
docker build .
cd ../..
```

When both the NWDAF and the FL service are running, you may invoke training and inference through the NWDAF's HTTP server, conveniently forwarded to the host's 24242 port.

```
curl https://localhost/nwdaf-mtlf/v1/:training -X POST
curl https://localhost/nwdaf-mtlf/v1/:inference -X POST
```

By default, both training and inference are over the WUSTL dataset. See [edusporto/fl-wsl](https://github.com/edusporto/fl-wsl) for more information.
