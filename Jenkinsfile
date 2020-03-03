properties([
    parameters([
           
        [$class: 'DynamicReferenceParameter',
          choiceType: 'ET_UNORDERED_LIST',
            description: 'Select Package',
            filterLength: 1,
            name: 'Packages',
            script: [
                $class: 'GroovyScript',
                fallbackScript: [
                    classpath: [],
                    sandbox: false,
                    script:
                        ''
                ],
                script: [
                    classpath: [],
                    sandbox: false,
                    script:
                        '''import jenkins.model.*

							instance = Jenkins.getInstance()
							globalNodeProperties = instance.getGlobalNodeProperties()

							myParam = 'NS'

							globalNodeProperties.each {
							  envVars = it.getEnvVars()
							  if (envVars.get('Packages') != null) {
							   myParam = envVars.get('Packages');
							  }
							}
                           command_output1 = myParam.replaceAll("[\\n\\r]", "")
                            List options = command_output1.split(',')
							return options
                        '''
                ]
            ]
        ],[$class: 'DynamicReferenceParameter',
           choiceType: 'ET_UNORDERED_LIST',
            description: 'Instances',
            filterLength: 1,
            name: 'ec2_amazon',
			 randomName: 'choice-parameter-15', 
            referencedParameters: 'ec2_amazon_List', 
            script: [
                $class: 'GroovyScript',
                fallbackScript: [
                    classpath: [],
                    sandbox: false,
                    script:
                        ''
                ],
                script: [
                    classpath: [],
                    sandbox: false,
                    script:
                        '''import jenkins.model.*

							instance = Jenkins.getInstance()
							globalNodeProperties = instance.getGlobalNodeProperties()

							myParam = 'NS'

							globalNodeProperties.each {
							  envVars = it.getEnvVars()
							  if (envVars.get('ec2_amazon') != null) {
							   myParam = envVars.get('ec2_amazon');
							  }
							}
                           command_output1 = myParam.replaceAll("[\\n\\r]", "")
                            List options = command_output1.split(',')
							return options
                        '''
                ]
            ]
        ],
		[$class: 'ChoiceParameter',
            choiceType: 'PT_SINGLE_SELECT',
            description: ' Select Actions ',
            filterLength: 1,
            name: 'Action',
            script: [
                $class: 'GroovyScript',
                fallbackScript: [
                    classpath: [],
                    sandbox: false,
                    script:
                        'return ["display","latest","present"]'
                ],
                script: [
                    classpath: [],
                    sandbox: false,
                    script:
                        'return ["display","latest","present"]'
                ]
            ]
        ]
    ])
])

pipeline {
     
  agent any
  stages {
      
stage ("Create Package List") {
        steps {
         script{
             sh label: '', script: '''#!/bin/bash
                echo ${params.Packages}
                echo "packages:" > main.yml
                package=($(echo ${Packages[@]} | sed 's/,/ /g'))
                for i in "${package[@]}"
                    do
                        echo "  - ${i}"  >> main.yml
                    done 
               cat main.yml
			   cp  main.yml ansible-patch/ansible-patch-role/vars/main.yml

                '''
      }
  }
}
stage ("Create Host File") {
        steps {
         script{
             sh label: '', script: '''#!/bin/bash
                rm -rf hosts
                echo ${ec2_amazon[@]}
                ec2_amazon_Choice=($(echo ${ec2_amazon[@]} | sed 's/,/ /g'))
                echo "${ec2_amazon_Choice}"
                arraylength=${#ec2_amazon_Choice[@]}
                echo "[patch-servers]" > hosts
                for (( i=0; i<${arraylength};i++));                  
                    do
                        echo "  ${ec2_amazon_Choice[$i]}"  >> hosts
                    done
                cat hosts 
				cp hosts ansible-patch/hosts'''
      }
  }
}
stage ("Run Ansible") {
        steps {
         script{
             sh label: '', script: '''#!/bin/bash
          ansible-playbook -i ansible-patch/hosts ansible-patch/main.yml -e "state=display" -u ec2-user --private-key=/home/jenkins/ec2_user_key/id_rsa
                '''
      }
  }
}

}
}
