# Fashion MNIST

Example of a CNN model trained on the Fashion MNIST dataset using TensorFlow.

## Prerequisites

Before attempting to run this example on Springfield ensure you've followed
the [_Getting Started_][getting-started] guide, which means you should have
access to the cluster file storage.

You need to have [`scp`][scp] or have mounted
your cluster file storage on your local machine using `sshfs` (or similar).

## Copying required files to the cluster

As part of running the example we'll have to copy some files to the cluster
file storage. There are many ways of doing this, but we recommend using an
approach that involves first downloading the files to your local machine
before transferring them to the cluster file storage using `scp`. Copying from
your local machine means it'll be easier to experiment by making changes, and
it also closer mimics a typical, everyday workflow.

The following snippet is a suggestion of how to setup a local workspace and
downloading the required example files.

```console
mkdir -p ~/Experiments/fashion-mnist
cd ~/Experiments/fashion-mnist
curl https://raw.githubusercontent.com/uitml/springfield/master/examples/tensorflow/fashion-mnist.{py,sh,yaml} -OOO
```

The command below copies all files to the home directory (`~/`) in the storage
proxy container, which is set to `/storage`. While this is OK for running this
example, we highly recommend that you find some type of structured directory
convention for your future experiments to make your life easier.

```console
scp -P <port> fashion-mnist.{py,sh} root@springfield.uit.no:~/
```

Note that the `<port>` placeholder above needs to be replaced with the SSH port
number unique to your cluster namespace. The easiest way to find that port is
to execute the following command.

```console
echo $(kubectl get svc -o jsonpath="{.items[?(@.metadata.name=='storage-proxy')]..nodePort}")
```

## Training the model

With the required files in place, the model can be trained by scheduling the
example job manifest defined in `fashion-mnist.yaml`. Scheduling is currently
supported by using `kubectl apply` and via `kubectl job run`, with the latter
being an extension provided by [Frink][frink].

```console
kubectl job run fashion-mnist.yaml
```

To monitor the model training, execute the following.

```console
kubectl job watch fashion-mnist
```

## Next steps

Currently the example does not persist the trained model state, which means
that all training is lost once the scheduled job finishes its execution.
Saving the trained model is left as an exercise for you to get familiar with
the cluster file storage (and TensorFlow). Keep in mind that any changes made
to the `/storage` directory (and its descendants) are persisted between jobs
since this is your personal cluster file storage. In other words, save the
trained model state somewhere in this directory.

Good luck!

<!--- References --->
[frink]: https://github.com/uitml/frink
[getting-started]: https://uitml.github.io/springfield/getting-started.html
[scp]: https://man.openbsd.org/scp.1
