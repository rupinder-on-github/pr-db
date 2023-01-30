pipeline{
    agent  any
        environment {
        APPLICATION = "d-bank"
    }
    
        stages{
        stage("Delete old workspace"){
            steps{
                cleanWs()
            }
        }
        stage("Clone main repository"){
            steps{
                echo ">======== Cloning code repository ========>"
                git branch: 'master',
                url: 'git@bitbucket.org:d-bank/code-scan.git'

            sh "ls -lat"
                
            }
            post{
                always{
                    echo "========always========"
                }
                success{
                    echo "========A executed successfully========"
                }
                failure{
                    echo ">========Failed cloning main repository deleting workspace fron jenkins agent ========>"
                    cleanWs()
                }
            }
        }
        stage("Clone data repos "){
            steps{
            sh '''
                 #!/bin/bash
                 set -x
                 mkdir Testresut 
                 for i in `cat project1.txt`
                 do
                 mkdir $i
                 echo "Executing $i repositories"
                 for j in `cat $i.txt`
                 do
                 git clone git@bitbucket.org:$i/$j.git  ${WORKSPACE}/$i/$j
                 ls -lrt ${WORKSPACE}/$i
                 cat pattern.txt
                 for k in `cat pattern.txt`
                 do 
                 echo "Pattern scanning started"
                 grep -lri $k ./$i/$j >> ./scanResult/$j_$k.txt || true 
                 echo "Pattern scanning success"
                 done 
                 echo "Deleting repository after scan result  for $j is saved  in result  file "
                 done
                 
                 done
                 cat ./scanResult/*.txt >> ./scanResult/NewResult.csv
                 
                 sort  ./scanResult/NewResult.csv > ./scanResult/SortedNewResult.csv
                 sort  ./scanResult/OldResult.csv > ./scanResult/SortedOldResult.csv
                 mv ./scanResult/SortedOldResult.csv ./scanResult/OldResult.csv
                 mv ./scanResult/SortedNewResult.csv ./scanResult/NewResult.csv
                 
                 comm -2   ./scanResult/OldResult.csv ./scanResult/NewResult.csv | awk '{$1=$1};1'
                 
                 comm -2 ./scanResult/OldResult.csv  ./scanResult/NewResult.csv | awk '{$1=$1};1' > ./scanResult/DiffResult.csv
                 rm -rvf ./scanResult/*.txt ./scanResult/OldResult.csv
                 mv ./scanResult/NewResult.csv ./scanResult/OldResult.csv
                 '''
            }
            post{
                always{
                    echo "====++++always++++===="
                }
                success{
                    echo "Clone data repos  stage passed !!!! "
                }
                failure{
                    echo ">========Failed cloning main repository deleting workspace fron jenkins agent ========>"
                    cleanWs()
                }
        
            }
        }
        stage("Publish the result back to  repo "){
            steps{
                echo "====++++executing Publish the result back to  repo ++++===="
                sh '''
                 echo "Pushing result from $BUILD_ID"
                 git config  user.email "you@example.com"
                 git config  user.name "test"
                 git add  scanResult
                 git commit -m "Added file from build $BUILD_ID" scanResult
                 git push --set-upstream origin master
                 echo "exit status of git add  is $?"

                  '''
            }
            post{
                always{
                    echo "====++++always++++===="
                }
                success{
                    echo "====++++Publish the result back to  repo  executed successfully++++===="
                }
                failure{
                    echo ">========Failed cloning main repository deleting workspace fron jenkins agent ========>"
                    cleanWs()
                }
        
            }
        }
    }
    post{
        always{
            echo "========always========"
        }
        success{
            echo "Build was success deleting the workspace."
            cleanWs()
        }
        
        failure{
            echo ">========Failed cloning main repository deleting workspace fron jenkins agent ========>"
                    cleanWs()
        }
    }
}
