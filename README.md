# aws-textract-DIO-Nexa-Bootcamp
A example project created with JAVA to demonstrate the AWS textract functionality, based in DIO BOOTCAMP

## This output has used the samples provided by AWS for JAVA that can be found on [examples AWS](https://github.com/awsdocs/aws-doc-sdk-examples/blob/main/javav2/example_code/textract/src/main/java/com/example/textract/AnalyzeDocument.java)


## Use Cases
I can see some use cases for this kind of solutions inside LIMS Systems, they're:

- Instrument Integrations
 - Some instruments generate only PDF files as output, using textract allows LIMS to interprete the PDF file and extract it data, parsing the results automatically to LIMS following the best practices of compliance.
 - Some instruments don't generate an output file that can be imported via network to the LIMS, like some kind of balances and ph meters, but the data is displayed to the user usually in a screen, user can take a picture and send this picture to be analyzed and get the results parsed automatically to LIMS.

- Dinamic Data creation: Samples, Batches, products, and instruments can be created based on a pdf or image.

- Pacients can be automatically registered, and their Consent forms can be automatically filled on BIOBANK LIMS after pacients fill them and take a picture or digitalize it.


## Details

This repo only contains the image processed and the output after execute the action AnalyzeDocument changing the doc source path to a local path.

I've also changed the print function to print the lines' text instead of the blocktype as it was on the original version of the code provided by AWS.

### Original Code

```java
// Copyright Amazon.com, Inc. or its affiliates. All Rights Reserved.
// SPDX-License-Identifier: Apache-2.0
public class AnalyzeDocument {
    public static void main(String[] args) {
        final String usage = """

                Usage:
                    <sourceDoc>\s

                Where:
                    sourceDoc - The path where the document is located (must be an image, for example, C:/AWS/book.png).\s
                """;

        if (args.length != 1) {
            System.out.println(usage);
            System.exit(1);
        }

        String sourceDoc = args[0];
        Region region = Region.US_EAST_2;
        TextractClient textractClient = TextractClient.builder()
                .region(region)
                .build();

        analyzeDoc(textractClient, sourceDoc);
        textractClient.close();
    }

    public static void analyzeDoc(TextractClient textractClient, String sourceDoc) {
        try {
            InputStream sourceStream = new FileInputStream(new File(sourceDoc));
            SdkBytes sourceBytes = SdkBytes.fromInputStream(sourceStream);

            // Get the input Document object as bytes
            Document myDoc = Document.builder()
                    .bytes(sourceBytes)
                    .build();

            List<FeatureType> featureTypes = new ArrayList<FeatureType>();
            featureTypes.add(FeatureType.FORMS);
            featureTypes.add(FeatureType.TABLES);

            AnalyzeDocumentRequest analyzeDocumentRequest = AnalyzeDocumentRequest.builder()
                    .featureTypes(featureTypes)
                    .document(myDoc)
                    .build();

            AnalyzeDocumentResponse analyzeDocument = textractClient.analyzeDocument(analyzeDocumentRequest);
            List<Block> docInfo = analyzeDocument.blocks();
            Iterator<Block> blockIterator = docInfo.iterator();

            while (blockIterator.hasNext()) {
                Block block = blockIterator.next();
                System.out.println("The block type is " + block.blockType().toString());
            }

        } catch (TextractException | FileNotFoundException e) {

            System.err.println(e.getMessage());
            System.exit(1);
        }
    }
}
```

### Adjusted Code

```java
        // Copyright Amazon.com, Inc. or its affiliates. All Rights Reserved.
        // SPDX-License-Identifier: Apache-2.0
        public class AnalyzeDocument {
        public static void main(String[] args) {
        final String usage = "C:\\Users\\GabrielAlves\\Desktop\\DIO - IA Generativa\\NEXA - Bootcamp\\lista-de-material-escolar.png";
        String sourceDoc = usage;
        Region region = Region.US_EAST_2;
        TextractClient textractClient = TextractClient.builder()
                .region(region)
                .build();

        analyzeDoc(textractClient, sourceDoc);
        textractClient.close();
    }

    public static void analyzeDoc(TextractClient textractClient, String sourceDoc) {
        try {
            InputStream sourceStream = new FileInputStream(new File(sourceDoc));
            SdkBytes sourceBytes = SdkBytes.fromInputStream(sourceStream);

            // Get the input Document object as bytes
            Document myDoc = Document.builder()
                    .bytes(sourceBytes)
                    .build();

            List<FeatureType> featureTypes = new ArrayList<FeatureType>();
            featureTypes.add(FeatureType.FORMS);
            featureTypes.add(FeatureType.TABLES);

            AnalyzeDocumentRequest analyzeDocumentRequest = AnalyzeDocumentRequest.builder()
                    .featureTypes(featureTypes)
                    .document(myDoc)
                    .build();

            AnalyzeDocumentResponse analyzeDocument = textractClient.analyzeDocument(analyzeDocumentRequest);
            List<Block> docInfo = analyzeDocument.blocks();
            Iterator<Block> blockIterator = docInfo.iterator();

            while (blockIterator.hasNext()) {
                Block block = blockIterator.next();
                if(block.blockType().toString().equals("LINE")){
                    System.out.println(block.text());
                }
            }

        } catch (TextractException | FileNotFoundException e) {

            System.err.println(e.getMessage());
            System.exit(1);
        }
    }
}
```