# jmeter-automation-tests
JMeter test plans and Jenkins pipeline for CI

## Run locally
jmeter -n -t test-plan.jmx -p jenkins.properties -l results/results.jtl

## Run with Jenkins
Pipeline reads Jenkinsfile and runs tests, generates HTML report in `results/report`.
