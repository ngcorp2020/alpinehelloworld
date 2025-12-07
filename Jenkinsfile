pipeline {
    environment {
	  IMAGE_NAME = "alpinehelloworld"
	  IMAGE_TAG  = "latest"
	  STAGING = "staging"
	  PRODUCTION = "production"
	}
	agent none
	stages {
	   stage('Build image'){
	      agent any 
		  steps {
		     script {
			   sh 'docker build -t ngcorp2020/$IMAGE_NAME:$IMAGE_TAG .'
			 }
		  }
		} 

	    stage('Run container base on build image'){
	      agent any 
		  steps {
		     script {
			   sh '''
			      docker run --name $IMAGE_NAME -d -p 80:5000 -e PORT=5000 ngcorp2020/$IMAGE_NAME:$IMAGE_TAG
				  sleep 5
			   
			   '''
			 }
		  }
		} 

        stage('Test image'){
	      agent any 
		  steps {
		     script {
			   sh '''
			      curl http://172.17.0.1 | grep -q "Hello world!"
			   
			   '''
			 }
		  }
		}
        
        stage('Clean container'){
	      agent any 
		  steps {
		     script {
			   sh '''
			      docker rm -f $IMAGE_NAME
			   
			   '''
			 }
		  }
		}
		 
		stage('push image in staging and deploy it') {
		  when {
		          expression {GIT_BRANCH == 'origin/master'}
		       }
		  agent any
		  environment {
		      HEROKU_API_KEY = credentials('heroku_api_key')
          }
          steps {
              script {
                sh '''
				 curl https://cli-assets.heroku.com/install.sh | sh
                 heroku container:login
				 heroku create $STAGING || echo "project already exist"
				 heroku containe:push -a $STAGING web
				 heroku container:release -a $STAGING web
				 '''
			  }
		  }
		
		}
		
		stage('push image in prod and deploy it') {
		  when {
		          expression {GIT_BRANCH == 'origin/master'}
		       }
		  agent any
		  environment {
		      HEROKU_API_KEY = credentials('heroku_api_key')
          }
          steps {
              script {
                sh '''
				 curl https://cli-assets.heroku.com/install.sh | sh
                 heroku container:login
				 heroku create $PRODUCTION || echo "project already exist"
				 heroku containe:push -a $PRODUCTION web
				 heroku container:release -a $PRODUCTION web
				 '''
			  }
		  }
		
		}
         		
	}
	
}
