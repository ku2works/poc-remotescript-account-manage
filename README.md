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
 --parameters commands="yum -y install python-pip; pip install ansible" \
 --region ap-northeast-1 \
 --profile \${profile} \

            command_id=`aws ssm send-command \
                --document-name "AWS-RunRemoteScript" \
                --instance-ids ${instanceId} \
                --parameters '{"sourceType":["GitHub"],"sourceInfo":["{\"owner\":\"ku2works\", \"repository\": \"poc-remotescript-account-manage\", \"path\": \"/\", \"getOptions\": \"branch:master\"}"],"commandLine":["ansible-playbook main.yml -i production"]}' \
                --region ap-northeast-1 \
                --profile ${profile} \
                | jq -r '.Command.CommandId'`

            aws ssm list-command-invocations \
                --command-id ${command_id} \
                --instance-id ${instanceId} \
                --region ap-northeast-1 \
                --profile ${profile} \
                --output text

            aws ssm get-command-invocation \
                --command-id ${command_id} \
                --instance-id ${instanceId} \
                --plugin-name runShellScript \
                --query "StandardOutputContent" \ \
                --region ap-northeast-1 \
                --profile ${profile} \
                --output text

        done

done
