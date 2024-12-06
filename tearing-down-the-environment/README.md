<h1>
  <span class="headline">Building a RESTful Microservice with Express</span>
  <span class="subhead">Tearing Down the Environment</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to safely and efficiently tear down cloud infrastructure using Terraform, ensuring resources are not left running unnecessarily.

## Tearing Down Your Infrastructure

As a best practice, it's essential to remove any cloud resources you no longer need. Leaving resources like your AWS database instance running can incur unexpected costs or create clutter in your cloud environment. Cleaning up ensures your infrastructure remains efficient and cost-effective.

### Navigate to the directory containing your Terraform configuration files.

Run the following command to begin the teardown process:

```sh
terraform destroy
```

- Terraform will display a summary of the resources it plans to delete and prompt you for confirmation. Type `yes` and press `Enter` to confirm.

- Wait for Terraform to complete the process. Once finished, all the resources defined in your configuration will be removed.

## Congratulations!

You've successfully completed the full lifecycle of building, deploying, and tearing down a RESTful microservice with modern cloud infrastructure.
