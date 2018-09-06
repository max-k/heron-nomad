# Intro

# Qu'est-ce que Nomad ?

# Qu'est-ce que Heron ?

# Démo 0 : Installation

<!-- docker system prune -af --volumes !>
wget https://github.com/apache/incubator-heron/releases/download/0.17.8/heron-install-0.17.8-ubuntu.sh
chmod +x heron-install-0.17.8-ubuntu.sh
./heron-install-0.17.8-ubuntu.sh --user
export PATH="$PATH:~/bin"
wget https://releases.hashicorp.com/nomad/0.8.4/nomad_0.8.4_linux_amd64.zip
unzip nomad_0.8.4_linux_amd64.zip
sudo cp nomad /usr/bin
chmod 755 /usr/bin/nomad

# Démo 1 : Nomad local

docker rm -f consul
docker run -tid --rm --net host --name=consul consul
sudo nomad agent -dev
nomad job init
nomad job run example.nomad
nomad job status example
nomad alloc status -stats xxxxxxxx
nomad alloc logs xxxxxxxx redis
firefox http://127.0.0.1:4646/ui/jobs
sed -i "s/\(count =\) 1/\1 3/" example.nomad
nomad job plan example.nomad
nomad job run -check-index 13 example.nomad
nomad job status example
sed -i "s/\(redis\):3.2/\1:4.0/" example.nomad
nomad job plan example.nomad
nomad job run -check-index 37 example.nomad
nomad job status example
nomad job stop example
nomad job status example

# Démo 2 : Heron local

heron submit local ~/.heron/examples/heron-api-examples.jar com.twitter.heron.examples.api.WindowedWordCountTopology --deploy-deactivated test
ls -al ~/.herondata/topologies/local/${USER}/test/WindowedWordCountTopology
ls -al ~/.herondata/topologies/local/${USER}/test/WindowedWordCountTopology/log-files
heron-tracker
heron-ui
heron activate local test
heron deactivate local test
heron kill local test

# Démo 3 : Heron dans Nomad

docker rm -f zookeeper
docker run -tid --rm --net host --name zookeeper zookeeper
cat > ~/.heron/conf/nomad/uploader.yaml << 'EOF'
# uploader class for transferring the topology jar/tar files to storage
heron.class.uploader:    com.twitter.heron.uploader.http.HttpUploader
heron.uploader.http.uri: http://localhost:9000/api/v1/file/upload
EOF
heron-apiserver --cluster nomad --base-template nomad --download-hostname 127.0.0.1 --heron-core-package-path ~/.heron/dist/heron-core.tar.gz -D heron.statemgr.connection.string=127.0.0.1:2181 -D heron.nomad.scheduler.uri=127.0.0.1:4647 -D heron.class.uploader=com.twitter.heron.uploader.http.HttpUploader --verbose
sed -i "s/#\(heron.package.core.*http:\/\/.*\)/\1/" ~/.heron/conf/nomad/client.yaml
sed -i "s/\(heron.package.core.*file:\/\/.*\)/#\1/" ~/.heron/conf/nomad/client.yaml


# Démo 4 : Heron standalone (embedded Nomad)

docker rm -f zookeeper
docker run -tid --rm --net host --name zookeeper zookeeper
heron-admin standalone set
heron-admin standalone cluster start
heron submit standalone ~/.heron/examples/heron-api-examples.jar com.twitter.heron.examples.api.WindowedWordCountTopology test
ln -s ~/.heron/bin/heron-nomad ~/bin/
heron-nomad job status
heron-nomad job status testxxxxxxxx
heron-nomad alloc-status xxxxxxxx
heron kill standalone test
heron-admin standalone cluster stop

# Démo 5 : Nomad cluster ?

# Démo 6 : Heron dans Nomad cluster ?

# Démo 7 : Cleanup

rm -rf ~/.pex ~/.herondata ~/.heron ~/bin/heron*

# Conclusion
