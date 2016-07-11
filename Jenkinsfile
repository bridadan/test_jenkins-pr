import org.jenkinsci.plugins.github.common.ExpandableMessage;

node('master') {
    stage 'Checkout'
    sh 'echo "Source file" > source.txt'
    archive 'source.txt'
    stage 'Build'
    def build_job_ret = build job: 'build_matrix', parameters: [
        [$class: 'StringParameterValue', name: 'checkout_project_name', value: env.JOB_NAME],
        [$class: 'StringParameterValue', name: 'checkout_build_number', value: env.BUILD_NUMBER]
    ]
    def build_job_message = new ExpandableMessage('Build Job')
    step([$class: 'GitHubCommitNotifier', statusMessage: build_job_message])
    stage 'Test'
    def test_job_ret = build job: 'test_matrix', parameters: [
        [$class: 'StringParameterValue', name: 'checkout_project_name', value: env.JOB_NAME],
        [$class: 'StringParameterValue', name: 'checkout_build_number', value: env.BUILD_NUMBER],
        [$class: 'StringParameterValue', name: 'build_project_name', value: "build_matrix"],
        [$class: 'StringParameterValue', name: 'build_build_number', value: build_job_ret.number.toString()]
    ]
    def test_job_message = new ExpandableMessage('Test Job')
    step([$class: 'GitHubCommitNotifier', statusMessage: test_job_message])
}
