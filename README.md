yum -y install python-pip
pip install ansible


git clone https://github.com/xxxxxxxx/xxxxxxxx.git
cd ./xxxxxxxx

ku2works/poc-remotescript-account-manage

INSTANCE_ID="i-0xxxxxxxxxxxxxxxx"
OWNER="ku2works"
REPOSITORY="poc-remotescript-account-manage"
DOWNLOAD_PATH="/"
GET_OPTIONS="branch:master"

PARAMETERS="{\"sourceType\":[\"GitHub\"],\"sourceInfo\":[\"{\\\"owner\\\":\\\"ku2works\\\", \\\"repository\\\": \\\"poc-remotescript-account-manage\\\", \\\"path\\\": \\\"/\\\", \\\"getOptions\\\": \\\"branch:master\\\"}\"],\"commandLine\":[\"ansible-playbook main.yml -i production\"]}" && echo ${PARAMETERS}

{"sourceType":["GitHub"],"sourceInfo":["{\"owner\":\"ku2works\", \"repository\": \"poc-remotescript-account-manage\", \"path\": \"/\", \"getOptions\": \"branch:master\"}"],"commandLine":["ansible-playbook main.yml -i production"]}

aws ssm send-command \
--document-name "AWS-RunShellScript" \
--instance-ids "i-05e9730458c436800" \
--parameters commands="yum -y install python-pip;pip install ansible" \

pip install ansible


    --query "Reservations[].Instances[?contains(to_string(Tags),'app_${client_name}_gyro')].InstanceId[]" \

?contains(to_string(Tags),'app_${client_name}_gyro')

export profiles="default ptp-hram ptp-shr ptp-ryobi-ap"
for profile in ${profiles};
do
    aws ec2 describe-instances \
        --query "Reservations[].Instances[].InstanceId[]" \
        --region ap-northeast-1 \
        --profile ${profile} \
        | for instanceId in $(jq -r '.[]');
        do
            aws ssm send-command \
                --document-name "AWS-RunShellScript" \
                --instance-ids ${instanceId} \
                --parameters commands="yum -y install python-pip;pip install ansible" \
                --region ap-northeast-1 \
                --profile ${profile} \

            aws ssm send-command \
                --document-name "AWS-RunRemoteScript" \
                --instance-ids ${instanceId} \
                --parameters '{"sourceType":["GitHub"],"sourceInfo":["{\"owner\":\"ku2works\", \"repository\": \"poc-remotescript-account-manage\", \"path\": \"/\", \"getOptions\": \"branch:master\"}"],"commandLine":["ansible-playbook main.yml -i production"]}' \
                --region ap-northeast-1 \
                --profile ${profile}

            aws ssm list-command-invocations \
                --command-id 7487f213-8d06-4fb9-bd27-36541c6c5f61 \
                --instance-ids ${instanceId} \
                --region ap-northeast-1 \
                --profile ${profile}

            aws ssm get-command-invocation \
                --command-id 7487f213-8d06-4fb9-bd27-36541c6c5f61 \
                --instance-ids ${instanceId} \
                --plugin-name runShellScript \
                --query "StandardOutputContent" \ \
                --region ap-northeast-1 \
                --profile ${profile}
                --output text

        done
done