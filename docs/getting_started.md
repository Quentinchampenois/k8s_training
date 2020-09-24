
# Known issues

Pods are pending 

Check node `kubectl get node`

Ensure status is not `Ready,SchedulingDisabled`. If it is the case run `kubectl uncordon <NODE_NAMe>`