yum -y install python-pip
pip install ansible


git clone https://github.com/xxxxxxxx/xxxxxxxx.git
cd ./xxxxxxxx

INSTANCE_ID="i-0xxxxxxxxxxxxxxxx"
OWNER="nakayamanobuhiro"
REPOSITORY="example-ansible-playbook-ssm"
DOWNLOAD_PATH="/"
GET_OPTIONS="branch:master"

PARAMETERS="{\"sourceType\":[\"GitHub\"],\"sourceInfo\":[\"{\\\"owner\\\":\\\"${OWNER}\\\", \\\"repository\\\": \\\"${REPOSITORY}\\\", \\\"path\\\": \\\"${DOWNLOAD_PATH}\\\", \\\"getOptions\\\": \\\"${GET_OPTIONS}\\\"}\"],\"commandLine\":[\"ansible-playbook -i \\\"localhost,\\\" -c local playbook.yml\"]}" && echo ${PARAMETERS}

{"sourceType":["GitHub"],"sourceInfo":["{\"owner\":\"nakayamanobuhiro\", \"repository\": \"example-ansible-playbook-ssm\", \"path\": \"/\", \"getOptions\": \"branch:master\"}"],"commandLine":["ansible-playbook -i \"localhost,\" -c local playbook.yml"]}

aws ssm send-command \
    --document-name "AWS-RunRemoteScript" \
    --instance-ids ${INSTANCE_ID} \
    --parameters "${PARAMETERS}"

aws ssm list-command-invocations \
    --command-id 4da78de8-5287-408d-8bd3-d7ab1a5411c4 \
    --instance-id ${INSTANCE_ID}
aws ssm get-command-invocation \
    --command-id 4da78de8-5287-408d-8bd3-d7ab1a5411c4 \
    --instance-id ${INSTANCE_ID} \
    --plugin-name runShellScript \
    --query "StandardOutputContent" \
    --output text