node {
   archive '**/*'
   stage 'Build'
   build job: 'build_matrix', parameters: [[$class: 'StringParameterValue', name: 'target', value: 'k64f lpc1768 nrf51_dk']]
   stage 'Test'
   build 'test_matrix'
}
