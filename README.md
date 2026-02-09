# Slurm integration for ColdFront

ColdFront django plugin providing Slurm REST API integration for ColdFront.

A command line tool is also provided with this app that allows an administrator
to check the consistency between ColdFront and Slurm and optionally remove any
associations that should not be in Slurm according to ColdFront.

## Design

Resources in ColdFront map to Clusters (or partitions within a cluster) in
Slurm. The name of the Slurm cluster is taken from a resource attribute in
ColdFront named "slurm\_cluster".  You can optionally provide Slurm
specifications for a cluster using a resource attribute named "slurm\_specs".
The value of this attribute must conform to the Slurm specification format and
are colon separated.

Allocations in ColdFront map to Accounts in Slurm. The name of the Slurm
account is taken from a allocation attribute in ColdFront named
"slurm\_account\_name" . You can optionally provide Slurm specifications for
the account using a allocation attribute named "slurm\_specs". The value of
this attribute must conform to the Slurm specification format and are colon
separated.

Allocation users in ColdFront map to Users in Slurm. You can optionally
provide Slurm specifications for each user in a allocation using a
allocation attribute named "slurm\_user\_specs". The value of this attribute
must conform to the Slurm specification format and are colon separated. Setting
specifications on an individual user basis is not currently supported.

## Usage

To enable this plugin set the following environment variables:

```
PLUGIN_SLURMREST=True
SLURMREST_CLUSTERS=production # add the names of the resources that correspond to the Slurm Clusters to use, separated by commas
# For each cluster listed in slurm_clusters:
# Set SLURM_{clustername}_ENDPOINT to the IP or URL, e.g.:
SLURM_PRODUCTION_ENDPOINT=http://production_cluster_url.com
# Set SLURM_{clustername}_TOKEN to the JWT token used for authentication
SLURM_PRODUCTION_TOKEN=$SLURMTEST_TOKEN
```


## Special cases

Resources in ColdFront can optionally be organized into parent/child
relationships. This is useful in the context of Slurm for example if you have a
single Slurm controller with multiple partitions. Each partition represents a
separate resource in ColdFront that users can subscribe to. In this case you
would create a parent resource that represents your Slurm cluster (or
controller) with a resource type of "Cluster". Each Slurm partition would be a
separate resource in ColdFront with a resource type of "Partition" and have
their parent resource set to the Slurm cluster. Users wouldn't subscribe to the
parent Slurm cluster resource but only subscribe to the partition resources. Here
you would only set the "slurm\_cluster" resource attribute on the Slurm cluster
resource and not on the partitions. Also, "slurm\_specs" resource attribute on
partitions are merged with the allocation "slurm\_specs" and set on the Slurm
account association instead of the cluster.
