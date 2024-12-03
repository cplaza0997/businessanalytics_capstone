
## Sequence Diagram - Adding new social network

```mermaid
sequenceDiagram
    participant u as User
    participant cec2 as Cluster EC2 (App)
    participant mdb as MongoDB
    participant papi as Pinterest API
    participant gapi as Google API
    participant fapi as Facebook API
    participant iapi as Instagram API

    u->>cec2: Link new social network
    cec2-->>u: Show list of social network availables
    u->>cec2: Select social network
    alt Is Fabook?
        cec2->>u: Ask for facebook permissitions.
        u-->>cec2: User selects an option
        alt Accept
            cec2->>fapi: Get facebook access
            fapi-->>cec2: Return message of acceptance and aditiona metadata
            cec2->>mdb: Store facebook metadata
        else Cancel
            cec2-->>u: Show message "Operation was canceled"
        end
    else

        alt Is Instagram?
            cec2->>u: Ask for Pinterest permissitions.
            u-->>cec2: User selects an option
            alt Accept
                cec2->>papi: Get Pinterest access
                papi-->>cec2: Return message of acceptance and aditional metadata
                cec2->>mdb: Store Pinterest metadata
            else Cancel
                cec2-->>u: Show message "Operation was canceled"
            end
        else
        
            alt Is Pinterest?
                cec2->>u: Ask for Instagram permissitions.
                u-->>cec2: User selects an option
                alt Accept
                    cec2->>iapi: Get Instagram access
                    iapi-->>cec2: Return message of acceptance and aditional metadata
                    cec2->>mdb: Store Instagram metadata
                else Cancel
                    cec2-->>u: Show message "Operation was canceled"
                end
            else
            
                alt Is Google?
                    cec2->>u: Ask for Google permissitions.
                    u-->>cec2: User selects an option
                    alt Accept
                        cec2->>gapi: Get Google access
                        gapi-->>cec2: Return message of acceptance and aditional metadata
                        cec2->>mdb: Store Google metadata
                    else Cancel
                        cec2-->>u: Show message "Operation was canceled"
                    end
                else
                
                end

            end

        end
    
    end
```


## Sequence Diagram - Storing images
```mermaid
sequenceDiagram
    participant u as User
    participant iapi as Instagram API
    participant cec2 as Cluster EC2 (App)
    participant mdb as MongoDB
    participant s3 as Bucket (S3)

    u->>iapi: Click on share resource
    iapi-->>u: Ask for share with
    u->>iapi: Select Unicorn Website
    iapi->>cec2: Send image object and certain metadata to Unicorn Website
    cec2->>s3: Store image object
    s3-->>cec2: message of success.
    cec2->>mdb: Store metadata related to image object and source.
    mdb-->>cec2: message of success.
    cec2-->>iapi: message of success. 
  
```


## Sequence Diagram - Data Replication
```mermaid
sequenceDiagram
    participant rep as Replication
    participant pdb as Postgres
    participant mdb as MongoDB
    participant s3 as Bucket (S3)

    par Postgres replication
        rep->>pdb: Ask for updates
        pdb-->>rep: Return updated data
        rep->>s3: Send updated data.
        s3-->>rep: Record logs. 
    and MongoDB replication
        rep->>mdb: Ask for updates
        mdb-->>rep: Return updated data
        rep->>s3: Send updated data.
        s3-->>rep: Record logs. 
    end
  
```

## Sequence Diagram - Feature extraction
```mermaid
sequenceDiagram
    participant emr as Elastic MapReduce
    participant mdb as MongoDB
    participant s3 as Bucket (S3)
    participant rek as Rekognition
    participant gdb as GraphDB

    emr->>s3: Ask for replicated data (Images and semistructed data).
    s3-->>emr: images and metadata
    emr->rek: Ask for objects detections inside the image
    rek-->>emr: list of words with contained objects in the image.
    emr->>emr: Add categories to images and relate them.
    emr->>gdb: Store relationship between similar images
    gdb-->>emr: message of success
    emr->>mdb: Store metadata
    mdb-->>emr: message of success

  
```

## Sequence Diagram - Recommendation system
```mermaid
sequenceDiagram
    participant u as User
    participant cec2 as Cluster EC2 (App)
    participant mdb as MongoDB
    participant s3 as Bucket (S3)
    participant gdb as GraphDB

    u->>cec2: Open the website
    cec2->>mdb: Get the user's favorite categories
    mdb-->>cec2: user's favorite categories
    cec2->>gdb: Ask for images related to user's favorite categories
    gdb-->>cec2: List of item IDs that user could like
    cec2->>s3: Ask for object images
    s3-->>cec2: object images.
    cec2-->>u: Show clothing images that they sell.
  
```