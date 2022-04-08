# delete-aws-s3-bucket-data

Code to delete s3-bucket:

import com.amazonaws.AmazonServiceException;
import com.amazonaws.auth.AWSStaticCredentialsProvider;
import com.amazonaws.auth.BasicAWSCredentials;
import com.amazonaws.regions.Regions;
import com.amazonaws.services.s3.AmazonS3;
import com.amazonaws.services.s3.AmazonS3ClientBuilder;
import com.amazonaws.services.s3.model.ListVersionsRequest;
import com.amazonaws.services.s3.model.ObjectListing;
import com.amazonaws.services.s3.model.S3ObjectSummary;
import com.amazonaws.services.s3.model.S3VersionSummary;
import com.amazonaws.services.s3.model.VersionListing;
 
public class DeleteBucket {
 
    public static void main(String[] args) {
 
        /* Create S3 Client Object */
        AmazonS3 s3 = AmazonS3ClientBuilder
                .standard()
                .withRegion(Regions.AP_SOUTH_1)
                .withCredentials(new AWSStaticCredentialsProvider(
                                      new BasicAWSCredentials("ACCESS_KEY","SECRET_KEY")))
                .build();
         
        try {   
            /* Get list of objects in a given bucket */
            ObjectListing objects = s3.listObjects("lb-aws-learning");
             
            /* Recursively delete all the objects inside given bucket */
            if(objects != null && objects.getObjectSummaries() != null) {               
                while (true) {                  
                    for(S3ObjectSummary summary : objects.getObjectSummaries()) {
                        s3.deleteObject("lb-aws-learning", summary.getKey());
                    }
                     
                    if (objects.isTruncated()) {
                        objects = s3.listNextBatchOfObjects(objects);
                    } else {
                        break;
                    }                   
                }
            }
             
            /* Get list of versions in a given bucket */
            VersionListing versions = s3.listVersions(new ListVersionsRequest().withBucketName("lb-aws-learning"));
             
            /* Recursively delete all the versions inside given bucket */
            if(versions != null && versions.getVersionSummaries() != null) {                
                while (true) {                  
                    for(S3VersionSummary summary : versions.getVersionSummaries()) {
                        s3.deleteObject("lb-aws-learning", summary.getKey());
                    }
                     
                    if (versions.isTruncated()) {
                        versions = s3.listNextBatchOfVersions(versions);
                    } else {
                        break;
                    }                   
                }
            }
 
            /* Send Delete Bucket Request */
            s3.deleteBucket("lb-aws-learning");
             
        } catch (AmazonServiceException e) {
             
            System.out.println(e.getErrorMessage());
             
        } finally {
             
            if(s3 != null) {
                s3.shutdown();
            }           
        }
    }
}




Code to delete s3-bucket data

	ObjectListing objects = s3Client.listObjects(bucketName);

		if(objects != null && objects.getObjectSummaries() != null) {               
			while (true) {                  
				for(S3ObjectSummary summary : objects.getObjectSummaries()) {
					s3Client.deleteObject(bucketName, summary.getKey());
				}

				if (objects.isTruncated()) {
					objects = s3Client.listNextBatchOfObjects(objects);
				} else {
					break;
				}                   
			}
		}
