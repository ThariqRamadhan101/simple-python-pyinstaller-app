node {
	stage('Build') {
		docker.image('python:2-alpine').inside {
			sh 'python -m py_compile sources/add2vals.py sources/calc.py'
		}
	}

	stage('Test') {
		docker.image('qnib/pytest').inside {
			try {
				sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
			} catch (Exception e) {
				echo 'Error: ' + e.toString()
			} finally {
				always {
					junit 'test-reports/results.xml'
				}

				input message: 'Lanjutkan ke tahap Deploy? (Click "Proceed" to continue)'
			}
		}
	}
    
	stage('Deploy') {
		try {
			sh "docker run --rm -v ${VOLUME} ${IMAGE} 'pyinstaller -F add2vals.py'"
		} catch (Exception e) {
			echo 'Error: ' + e.toString()
		} finally {
			success {
				archiveArtifacts 'dist/add2vals'
				sh "docker run --rm -v ${VOLUME} ${IMAGE} 'rm -rf build dist'"
				sleep 60
			}
		}
	}
}