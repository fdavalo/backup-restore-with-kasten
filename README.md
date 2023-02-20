# Backup/Restore with Kasten

## Setup for the demo

* Two openshift clusters (bm2 and bm3)

[![clusters-bm2-bm3](https://github.com/fdavalo/backup-restore-with-kasten/blob/main/acm-bm2-bm3.png?raw=true)](acm-bm2-bm3.png)

* Install operator Kasten 
  
    cf https://github.com/fdavalo/cluster-configuration/tree/main/applications/static-clusters-post-install/kasten
  
    cf https://github.com/fdavalo/cluster-configuration/tree/main/applications/static-clusters-post-install/kasten-instance

* Install operator Open Data Foundation (using file and block storage for application persistence, and s3 for exporting backup data)
  
    cf https://github.com/fdavalo/cluster-configuration/tree/main/applications/post-install/odf
  
    cf https://github.com/fdavalo/cluster-configuration/tree/main/applications/post-install/odf-instance

* Install an application on bm3 for demonstration (here gogs-helm, a github like application) with fie and block persisten storage claims
  
       project: default
       source:
         repoURL: 'https://github.com/fdavalo/gogs.git'
         path: chart
         targetRevision: HEAD
       destination:
         server: 'https://api.bm3.redhat.hpecic.net:6443'
         namespace: gogs-helm-bm3
       syncPolicy:
         syncOptions:
           - CreateNamespace=true

[![kasten-application-pods](https://github.com/fdavalo/backup-restore-with-kasten/blob/main/kasten-application-pods.png?raw=true)](kasten-application-pods.png)

[![kasten-application-pvc](https://github.com/fdavalo/backup-restore-with-kasten/blob/main/kasten-application-pvc.png?raw=true)](kasten-application-pvc.png)

## Connect to Kasten UI and setup a backup location (bucket S3)

## Backup the application with Kasten (on bm3)

* Create a backup policy for application gogs-helm

[![kasten-create-backup-policy](https://github.com/fdavalo/backup-restore-with-kasten/blob/main/kasten-create-backup-policy.png?raw=true)](kasten-create-backup-policy.png)

* Start the backup of the application (manually, not scheduled)

[![kasten-backup-started](https://github.com/fdavalo/backup-restore-with-kasten/blob/main/kasten-backup-done.png?raw=true)](kasten-backup-done.png)

* See the end of the backup and the export on S3 location, the result of the backup (crypted restore points)

[![kasten-backup-done](https://github.com/fdavalo/backup-restore-with-kasten/blob/main/kasten-backup-started.png?raw=true)](kasten-backup-started.png)

* See snapshots have been created for persistent volume claims of the application gogs-helm

[![kasten-backup-volume-snapshot-content](https://github.com/fdavalo/backup-restore-with-kasten/blob/main/kasten-backup-volume-snapshot-content.png?raw=true)](kasten-backup-volume-snapshot-content.png)

[![kasten-backup-volume-snapshot](https://github.com/fdavalo/backup-restore-with-kasten/blob/main/kasten-backup-volume-snapshot.png?raw=true)](kasten-backup-volume-snapshot.png)

## Restore the application with Kasten (on bm2)

* Ensure the namespace is created for gogs-helm on bm2 (empty)

* Create a policy to import backup restore points

[![kasten-create-import-policy](https://github.com/fdavalo/backup-restore-with-kasten/blob/main/kasten-create-import-policy.png?raw=true)](kasten-create-import-policy.png)

* Fetch the passphrase from the backup policy (on bm3)

[![kasten-backup-policy-export-passphrase](https://github.com/fdavalo/backup-restore-with-kasten/blob/main/kasten-backup-policy-export-passphrase.png?raw=true)](kasten-backup-policy-export-passphrase.png)

* Create a transformation to change routes hostname (from bm3 to bm2)

[![kasten-transform-routes](https://github.com/fdavalo/backup-restore-with-kasten/blob/main/kasten-transform-routes.png?raw=true)](kasten-transform-routes.png)

* Add transformation to the restore policy

[![kasten-transform](https://github.com/fdavalo/backup-restore-with-kasten/blob/main/kasten-transform.png?raw=true)](kasten-transform.png)

* Launch the import of restore points

[![kasten-import-backup-restore-points](https://github.com/fdavalo/backup-restore-with-kasten/blob/main/kasten-import-backup-restore-points.png?raw=true)](kasten-import-backup-restore-points.png)

* Find restore points for application gogs-helm

[![kasten-restore-ready](https://github.com/fdavalo/backup-restore-with-kasten/blob/main/kasten-restore-ready.png?raw=true)](kasten-restore-ready.png)

* Start restoring from a choosen restore point for gogs-helm

[![kasten-restore-start](https://github.com/fdavalo/backup-restore-with-kasten/blob/main/kasten-restore-start.png?raw=true)](kasten-restore-start.png)


