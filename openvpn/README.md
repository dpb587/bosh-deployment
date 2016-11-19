example to colocate with bosh-lite; VMs/containers directly addressable

    bosh \
      create-env \
      bosh.yml \
      --ops-file use-aws.yml \
      --ops-file enable-bosh-lite.yml \
      --ops-file openvpn/enable.yml \
      --vars-store vars-generated.yml \
      --vars vars-user.yml

    openvpn -c <(
      bosh \
        build-manifest \
        openvpn/profile.ovpn \
        --vars-store vars-generated.yml \
        --vars vars-user.yml
        --path /profile
    )

    bosh \
      --ca-cert <( bosh build-manifest vars.yml --path /default_ca/certificate  ) \
      --environment "$( bosh build-manifest secrets.yml --path /internal_ip )" \
      alias-env \
      bosh-lite

    export BOSH_ENVIRONMENT=bosh-lite
    export BOSH_PASSWORD="$( bosh build-manifest vars.yml --path /admin_password )"

    bosh --user admin log-in

    unset BOSH_PASSWORD
