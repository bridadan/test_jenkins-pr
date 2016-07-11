import groovyx.net.http.HTTPBuilder
import static groovyx.net.http.ContentType.*
import static groovyx.net.http.Method.*


node('master') {
    stage 'Checkout'
    checkout scm
    sh 'git rev-parse HEAD > commit.txt'
    def GITHUB_COMMIT = readFile 'commit.txt'
    GITHUB_COMMIT = GITHUB_COMMIT.replaceAll("\\s","")
    sh 'echo "' + GITHUB_COMMIT + ' is done"'
    sh 'git show ' + GITHUB_COMMIT + '^1 --pretty=format:%H | head -n 1'
    sh 'git show ' + GITHUB_COMMIT + '^1 --pretty=format:%H --no-notes | head -n 1 > parent_commit.txt'
    def parent_commit = readFile 'parent_commit.txt'
    parent_commit = parent_commit.replaceAll("\\s","")
    sh 'echo "Source file" > source.txt'
    archive 'source.txt'
    
    echo parent_commit
    stage 'Build'
    def build_job_ret = build job: 'build_matrix', parameters: [
        [$class: 'StringParameterValue', name: 'checkout_project_name', value: env.JOB_NAME],
        [$class: 'StringParameterValue', name: 'checkout_build_number', value: env.BUILD_NUMBER],
        [$class: 'StringParameterValue', name: 'GITHUB_COMMIT', value: parent_commit]
    ]


    def http = new HTTPBuilder('http://github.com/repos/bridadan/test_jenkins-pr/statuses/' + parent_commit)
    http.request( POST ) {
        uri.path = '/'
        requestContentType = ContentType.JSON

        body =  [
            state: 'success', 
            target_url: 'http://google.com',
            description: "Job succeeded",
            "context": "Build Job"
        ]
    
        response.success = { resp ->
            println "POST response status: ${resp.statusLine}"
            assert resp.statusLine.statusCode == 201
        }
    }
    
    stage 'Test'
    def test_job_ret = build job: 'test_matrix', parameters: [
        [$class: 'StringParameterValue', name: 'checkout_project_name', value: env.JOB_NAME],
        [$class: 'StringParameterValue', name: 'checkout_build_number', value: env.BUILD_NUMBER],
        [$class: 'StringParameterValue', name: 'build_project_name', value: "build_matrix"],
        [$class: 'StringParameterValue', name: 'build_build_number', value: build_job_ret.number.toString()],
        [$class: 'StringParameterValue', name: 'GITHUB_COMMIT', value: parent_commit]
    ]
    
    http.request( POST ) {
        uri.path = '/'
        requestContentType = ContentType.JSON

        body =  [
            state: 'success', 
            target_url: 'http://yahoo.com',
            description: "Job succeeded",
            "context": "Test Job"
        ]
    
        response.success = { resp ->
            println "POST response status: ${resp.statusLine}"
            assert resp.statusLine.statusCode == 201
        }
    }
}
