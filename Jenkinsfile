node('builder'){
    docker.image('diogorac/rnc_builder').inside('--privileged') {
        checkout scm
        stage('Generating build') {
            sh 'mkdir -p build && cd build && cmake ../ -DTARGET_GROUP=test -DSTATIC_ANALYSIS=1  '
        }
        dir('build')
        {
            stage('Coding Guideline')
            {
                sh 'astyle "src/*.c" "include/*.h" --style=google -s2'
                sh 'if [ $(find . -iname "*\.orig" | wc -l) != 0 ]; then echo "According to guideline."; else exit(1); fi'
            }
            stage('Build') {
                sh 'make'
            }
            stage('Testing') {
                sh 'make check'
                sh 'xsltproc /opt/ctest/ctest2junix.xsl tests/Testing/$(head -1 tests/Testing/TAG)/Test.xml > CTestResults.xml '
                junit 'CTestResults.xml'
                cobertura coberturaReportFile: 'coverage.xml'
            }
        }
    }
}
