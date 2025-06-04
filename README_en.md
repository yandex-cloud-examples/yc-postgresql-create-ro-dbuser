# Creating a PostgreSQL instance, database, and users, and granting read-only permissions to users 

### Why do you need this? 

In Managed PostgreSQL, you use the Yandex Cloud API to create a user and PostgreSQL, to grant them permissions. In this example, we will show you how to create a DB user and grant them permissions using a single tool, Terraform.

### Requirements

* Yandex Cloud command-line utility: [yc](https://cloud.yandex.com/docs/cli/quickstart).
* Cloud (cloud\_id) and folder (folder\_id).
* PostgreSQL console client (`psql`).

> Note: In this example, we create the PostgreSQL instance with a public address.<br/>
> Do not use this setup in the production environment.

### Description

While following this example, you will be creating the following objects in the Yandex Cloud infrastructure:

* VPC.
* Subnet.
* Security group.
* Managed PostgreSQL.
* `user_owner` PostgreSQL user (database owner).
* `db1` PostgreSQL database.
* Database objects (tables with test data).
* `user_ro` PostgreSQL user (read-only).
* `SELECT` permissions for all `public` schema objects granted to `user_ro`.

### Preparation and running

1. Create the environment:

    ```
    export YC_TOKEN=$(yc iam create-token)
    export TF_VAR_cloud_id=$(yc config get cloud-id)
    export TF_VAR_folder_id=$(yc config get folder-id)
    ```
	
1. Generate two passwords:

    ```
    export TF_VAR_user_owner_passwd=$(openssl rand -base64 12)
    export TF_VAR_user_ro_passwd=$(openssl rand -base64 12)  
    ```
	
1. Initialize Terraform:
 
    ```
    terraform init
    ```
	
1. Create configuration:

    ```
    terraform apply
    ```
	
1. Verify the created objects and permissions: 
   
    Set up the database connection. The name of the DB host will appear in `Outputs` once the configuration is created.

    ```
    export PGHOST=<DB_host_name>
    ```

    ```
    export PGPORT=6432
    export PGPASSWORD="$TF_VAR_user_ro_passwd"
    
    ```

    Connect to a database:

    ```
    psql db1 user_ro
    ```
    
    Check the permissions:
    
    ```
    SELECT * FROM "movies"; -- Success
    DELETE FROM "movies"; -- `Permission denied` error
    ```
	
1. Delete the created resources:

    ```
    terraform destroy
    ```


