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

  // Third stage: Manual Approval
  stage('Manual Approval') {
      // Confirmation before deployment
      input message: 'Lanjutkan ke tahap Deploy?'
  }

  // Fourth stage: Deploy
  stage('Deploy') {

    // Utilising docker as the agent with minidocks/pyinstaller as the image
    docker.image('minidocks/pyinstaller:6').inside {
      // Create executable file
      sh 'pyinstaller --onefile ./sources/add2vals.py'

      // Try to running app (sleep in 1 minute)
      echo 'Try to execute with 10 and 20 as params'
      sh './dist/add2vals 10 20'
      sleep 60

      // Store as jenkins artifact
      archiveArtifacts artifacts: 'dist/add2vals'

      // Upload to Google Cloud Storage
      step([
        $class: 'ClassicUploadStep',
        credentialsId: 'gcs_credential',
        bucket: "gs://faruqabdulhakim-bucket",
        pattern: 'dist/add2vals'
      ])
    }
  }
}