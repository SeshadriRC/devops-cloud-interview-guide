1. Your AWS CodeBuild project fails because an external dependency (e.g., an NPM package) is not found. How can you fix it?

- Check the buildspec.yml file to ensure dependencies are installed before the build

Why:

buildspec.yml defines the build phases in CodeBuild.

Dependencies should usually be installed in the install or pre_build phase.

---

2. A customer-facing Al API runs as an ECS service behind a load bala recovery. Which deployment approach is most suitable?

- Use CodeDeploy with two ECS task sets, For customer-facing ECS applications requiring minimal downtime and quick rollback, use CodeDeploy with Blue/Green deployment using two ECS task sets behind a load balancer.

---

3. You deploy a new version using AWS CodeDeploy, but the deployment rolls back. As a developer, how can you troubleshoot?

- Check the AWS CodeDeploy logs in Amazon CloudWatch

---

4. You are designing a Dynamo DB datastore to record electric meter readings from millions of home once in a week, We sh so the week must be part of primary Key. How might we design our datastore for Optimal efficiency?

- Use large table for all the data and create Global secondary index on week.

---

5. You update code in AWS CodeCommit repository, but CodePipeline does NOT trigger a build. What should you check?

- Check if the source stage in CodePipeline is correctly linked to CodeCommit

---
