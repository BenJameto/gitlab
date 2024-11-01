
# Despliegue de GitLab en Minikube

Esta es una guía que detalla el paso a paso para desplegar GitLab en Minikube, incluyendo la configuración los volumenes persistentes y algunos problemas resueltos que salieron sobre la marcha.

**Nota:**

1. Para GitLab, se recomienda designar al menos **4-6 GB de RAM** y **2-4 CPUs**.

2. Para que esto funcione necesitas que *minikube* este istalado y corriendo. a demas de crear un nuevo namespace por ejemplo:
```bash
kubectl create new namespace gitlab
```
## Pasos

1. Clonar el este repositorio.


2. Posisionate en la raiz del repo:

```bash
cd tu_direccion/gitlab
```

3. Aplica el archivo llamado *gitlab-pvc.yaml*

- Para asegurar que los datos de GitLab persistan entre reinicios, es que se define Persistent Volume Claims (PVCs). Uno para datos, otro para logs y otro para configuración.

 `gitlab-pvc.yaml`:


Aplica la configuración del PVC:

```bash
kubectl apply -f gitlab-pvc.yaml -n gitlab
```

4. Aplica el archivo llamado `gitlab-deployment.yaml` con:


```bash
kubectl apply -f gitlab-deployment.yaml -n gitlab
```


## Acceder a GitLab

- Ejecuta `minikube ip` para obtener la dirección IP de Minikube.
- Accede a GitLab vía `http://<minikube-ip>:30080`.

## Observaciones:

- **Restablecimiento de Contraseña**: Para restablecer la contraseña de `root`, accede al contenedor de GitLab y configúrala manualmente:
  ```bash
  kubectl exec -it <gitlab-pod-name> -n gitlab -- /bin/bash
  gitlab-rails runner "user = User.where(id: 1).first; user.password = 'newpassword'; user.password_confirmation = 'newpassword'; user.save!"
  ```

- **GITLAB_OMNIBUS_CONFIG**: Esto configura la URL externa y el puerto SSH de GitLab. Ajusta `<minikube-ip>` y `gitlab_rails['gitlab_shell_ssh_port']` según tu configuración.
