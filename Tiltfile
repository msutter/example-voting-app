allow_k8s_contexts('default/api-crc-testing:6443/kube:admin')

# Deploy: tell Tilt what YAML to deploy
# k8s_yaml('app.yaml')

k8s_yaml('k8s-specifications/db-deployment.yaml')
k8s_yaml('k8s-specifications/db-service.yaml')

k8s_yaml('k8s-specifications/redis-deployment.yaml')
k8s_yaml('k8s-specifications/redis-service.yaml')

k8s_yaml('k8s-specifications/vote-deployment.yaml')
k8s_yaml('k8s-specifications/vote-service.yaml')

k8s_yaml('k8s-specifications/result-deployment.yaml')
k8s_yaml('k8s-specifications/result-service.yaml')

k8s_yaml('k8s-specifications/worker-deployment.yaml')

# Build: tell Tilt what images to build from which directories
docker_build('msutter/examplevotingapp_vote', './vote')
docker_build('msutter/examplevotingapp_result', './result')
docker_build('msutter/examplevotingapp_worker', './worker')

# Watch: tell Tilt how to connect locally (optional)
k8s_resource('vote', port_forwards=31000)
k8s_resource('result', port_forwards=31001)