# -FortiSIEM-Can-t-deploy-FortiSIEM-VA.ova-in-vSphere-Client-5.5
Unable to deploy FortiSIEM-VA.ova on vSphere Client 5.5. During deployment, the process fails because vSphere Client 5.5 does not support SHA256-signed OVF/OVA packages. Since FortiSIEM 7.x images are signed with SHA256, older clients like vSphere 5.5 cannot validate and import them.
