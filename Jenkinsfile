import org.jenkinsci.plugins.github.common.ExpandableMessage;

node('master') {
    stage 'Checkout'
    checkout scm
    sh 'git rev-parse HEAD > commit.txt'
    def GITHUB_COMMIT = readFile 'commit.txt'
    sh 'echo ' + ${GITHUB_COMMIT} + ' is done'
    sh 'git rev-list ${GITHUB_COMMIT} --parents -n 1'
    sh 'git rev-list ${GITHUB_COMMIT} --parents -n 1 > commit.txt'
    sh 'echo "Source file" > source.txt'
    archive 'source.txt'
    
    echo GITHUB_COMMIT
    stage 'Build'
    def build_job_ret = build job: 'build_matrix', parameters: [
        [$class: 'StringParameterValue', name: 'checkout_project_name', value: env.JOB_NAME],
        [$class: 'StringParameterValue', name: 'checkout_build_number', value: env.BUILD_NUMBER],
        [$class: 'StringParameterValue', name: 'GITHUB_COMMIT', value: GITHUB_COMMIT]
    ]
    stage 'Test'
    def test_job_ret = build job: 'test_matrix', parameters: [
        [$class: 'StringParameterValue', name: 'checkout_project_name', value: env.JOB_NAME],
        [$class: 'StringParameterValue', name: 'checkout_build_number', value: env.BUILD_NUMBER],
        [$class: 'StringParameterValue', name: 'build_project_name', value: "build_matrix"],
        [$class: 'StringParameterValue', name: 'build_build_number', value: build_job_ret.number.toString()],
        [$class: 'StringParameterValue', name: 'GITHUB_COMMIT', value: GITHUB_COMMIT]
    ]
}
