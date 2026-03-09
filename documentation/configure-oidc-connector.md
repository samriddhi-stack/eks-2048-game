# Configure IAM OIDC provider

<pre><code>export cluster_name=demo-cluster</code></pre>
<pre><code>oidc_id=$(aws eks describe-cluster --name $cluster_name --query "cluster.identity.oidc.issuer" --output text | cut -d '/' -f 5) </code></pre>
