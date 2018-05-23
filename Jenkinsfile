#!/usr/bin/env groovy
import groovy.transform.Field

@Field String DOCKER_IMAGE_CHAOS = "mlafeldt/simianarmy"

properties([
    parameters([[
        $class: 'CredentialsParameterDefinition',
        credentialType: 'com.cloudbees.plugins.credentials.impl.UsernamePasswordCredentialsImpl',
        defaultValue: 'ld-terraform-staging-secret',
        description: 'Where to unleash chaos?',
        name: 'AWS_CREDS',
        required: true
    ], string(
        name: 'CALENDAR_ISMONEYTIME',
        description: 'Make the monkeys work?',
        defaultValue: 'true',
        required: true
    ), string(
        name: 'CHAOS_ASG_ENABLED',
        description: 'All trees have banana or only some?',
        defaultValue: 'true',
        required: true
    )])
])

node {

  stage('SCM Checkout') {
    checkout scm
  }

  stage('Dry Run Chaos Monkey') {
    withCredentials([
      usernamePassword(credentialsId: 'ld-terraform-staging-secret', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')
    ]) {
      docker.image(DOCKER_IMAGE_CHAOS).inside("--entrypoint \"\" -u root -v \"${pwd()}\":/tmp -w /tmp") {
        sh """
          export SIMIANARMY_CLIENT_AWS_ACCOUNTKEY=${env.AWS_ACCESS_KEY_ID}
          export SIMIANARMY_CLIENT_AWS_SECRETKEY=${env.AWS_SECRET_ACCESS_KEY}
          export SIMIANARMY_CLIENT_AWS_REGION=us-west-2
          export SIMIANARMY_CALENDAR_ISMONKEYTIME=${env.CALENDAR_ISMONKEYTIME}
          export SIMIANARMY_CHAOS_ASG_ENABLED=${env.CHAOS_ASG_ENABLED}
        """
      }
    }
  }
}