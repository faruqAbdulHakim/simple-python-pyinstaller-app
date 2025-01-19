node {
  // Checkout the source code from SCM
  checkout scm

  // First stage: Build
  stage('Build') {

    // Utilising docker as the agent with python2 as the image
    docker.image('python:2-alpine').inside {

      // Compile python code
      sh 'python -m py_compile sources/add2vals.py sources/calc.py'
    }
  }

  // Second stage: Test
  stage('Test') {
    
    // Utilising docker as the agent with qnib/pytest as the image
    docker.image('qnib/pytest').inside {

      try {
        // Execute test
        sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
      } finally {
        // Ensure junit still executed
        junit 'test-reports/results.xml'
      }

    }
  }
}