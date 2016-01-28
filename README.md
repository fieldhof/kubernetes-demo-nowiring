# Kubernetes-demo-cluster addition for including phones

This is a small addition to the already existing kubernetes-demo-cluster from INAETICS.
For use, first follow the instructions on the page of the kubernetes-demo-cluster located [here](https://github.com/INAETICS/kubernetes-demo-cluster "Kubernetes-demo-cluster")

---

# Steps for adding phones
(all relative paths are relative to your kubernetes-demo-cluster directory)

1. Find out what ip address your Controller has `ip a` (not the 172.17.8.20)
1. Copy all the contents in this repository to the kubernetes-demo-cluster directory
2. Copy the `felix-agent.tar` and `celix-agent.tar` located at `Controller/inaetics-demo/images/` to `image_edit/felix` and `image_edit/celix` respectively
3. Edit `image_edit/felix/node-agent.sh` to your own liking (Check [here](#config_node-agent) for example)
4. Load one of the images in docker `docker load -i image_edit/felix/felix-agent.tar`
5. Put the new `image_edit/felix/node-agent.sh` in this image `docker build -t 172.17.8.20:5000/inaetics/felix-agent .` (<- Dont forget the dot)
6. Save the image `docker save -o Controller/inaetics-demo/felix-agent.tar 172.17.8.20:5000/inaetics/felix-agent`
7. Repeat step 3 - 6 for celix (Change felix to celix in the steps)
8. You now have to add your phone bundles to Apache Ace. This can be done by placing them in Controller/inaetics-demo/bundles/default-resources. The bundles need to be .jar files. Check [here](https://github.com/bpetri/nativeLoad/wiki/Folder:-apache_ace_config) how to do that get a .zip to a .jar file (Scroll down to bundle-renamer/ziptojar.sh)
9. Edit the default-mapping.gosh so the phone bundles are included as wel. Make sure to create the features, distribution and targets. Name the targets so the cpu architecture is in the id and link them to the correct bundles.

# Configuring node-agent.sh <a name="config_node-agent"></a>
For felix, you need to change the following configuration in felix/node-agent.sh but only when it's a queue, this is when $agent_id = felix_2
- org.amdatu.remote.admin.http.host=[ip_of_controller]
- org.amdatu.remote.admin.http.port=87

For celix, you need to change the following configuration in celix/node-agent.sh but only when it's a queue, this is when $agent_id = celix_2
- DISCOVERY_CFG_SERVER_IP=[ip_of_controller]
- DISCOVERY_CFG_SERVER_PORT=85
- RSA_IP=[ip_of_controller]
- RSA_PORT=86

---
#Troubleshooting
-Sometimes the celix queue will not work correctly with felix producers/processors and the other way around. Try disabling celix queue and starting a felix queue or the other way around.
- The wiring is not yet fully supported and the phones, so you will need to disable the rsa_wiring bundles in ace and going back to rsa_http bundles. This can be done in the GUI in runtime, or in the default-mapping.gosh file located Controller/inaetics-demo/bundles
