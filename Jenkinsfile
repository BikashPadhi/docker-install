#!groovy
properties(
  [
    buildDiscarder(logRotator(numToKeepStr: '20')),
    parameters([
      string(defaultValue: '', description: 'Expected docker version output of --version flag.', name: 'EXPECTED_VERSION'),
      string(defaultValue: '', description: 'Expected docker gitcommit output of --version flag.', name: 'EXPECTED_GITCOMMIT'),
    ])
  ]
)

def verifyTargets = [
  'verify-install-centos-7',
  'verify-install-fedora-24',
  'verify-install-fedora-25',
  'verify-install-debian-wheezy',
  'verify-install-debian-jessie',
  'verify-install-debian-stretch',
  'verify-install-ubuntu-trusty',
  'verify-install-ubuntu-xenial',
  'verify-install-ubuntu-yakkety',
  'verify-install-ubuntu-zesty',
]

def genVerifyJob(String t) {
  return [ "${t}" : { ->
    stage("${t}") {
      wrappedNode(label: 'aufs', cleanWorkspace: true) {
        checkout scm
        channel = 'test'
        if ("${env.JOB_NAME}".endsWith('get.docker.com')) {
            channel='edge'
        }
        sh("make CHANNEL_TO_TEST=${channel} clean ${t}.log")
        archiveArtifacts '*.log'
      }
    }
  } ]
}

def verifyJobs = [:]
for (t in verifyTargets) {
  verifyJobs << genVerifyJob(t)
}

parallel(verifyJobs)
