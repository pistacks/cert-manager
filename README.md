# cert-manager
Cert Manager for ARM

## Usage

Create the namespace:

```
$ kubectl create namespace cert-manager
```

The manifest in this repository is from this [source](https://github.com/jetstack/cert-manager/releases/download/v0.11.0/cert-manager.yaml), only the images has been modified to `-arm` to enable it to work for arm. You can either use this manifest, or the original and just update the image names to work:

```
$ 
```
