# Killer.sh

## Questions and Answers

### Question 01

Task weight: 1%

You have access to muliple clusters from your main terminal through `kubectl` contexts. Write all those context names into `/opt/course/1/contexts` .

Next write a command to display the current context into `/opt/course/1/context_default_kubectl.sh`, the command should use `kubectl`.

Finally write a second command doing the same thing into `/opt/course/1/context_default_no_kubecth.sh`, but without the use of `kubectl`.

#### Answer

```bash
export do="--dry-run=client -o yaml"

k config get-contexts -o name

k config get-contexts -o name > /opt/course/1/contexts

kubectl config current-context

vi /opt/course/1/context_default_kubectl.sh

kubectl config current-context

vi /opt/course/1/context_default_no_kubectl.sh

cat ~/.kube/config | grep current-context | awk -F ' ' '{print $2}'


### Question 02

Task weight: 3%

Use context: kubectl config use-context k8s-c1-H

Create a single *Pod* of image `httpd:2.4.41-alpine` in *Namespace* `default`. The Pod should be named `pod1` and the container should be named `pod1-container`. This *Pod* should **only** be scheduled on a master node*, do not add new labels any nodes.


#### Answer

kubectl config use-context k8s-c1-H

k run pod1 --image httpd:2.4.41-alpine $do > 2.yaml

vi 2.yaml

