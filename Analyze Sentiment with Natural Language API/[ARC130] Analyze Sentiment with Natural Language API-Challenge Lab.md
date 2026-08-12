# [ARC130] Analyze Sentiment with Natural Language API: Challenge Lab

### `🔗 Lab Link` - [*Click Here*](https://www.skills.google/course_templates/667/labs/599108)

## Task 1. Create an API key
1. In the Cloud Console, select **Navigation menu > APIs & Services > Credentials**.
2. Click Create credentials and select **API key**.
3. In the Create API key side panel, under APIs that can be accessed using this key, for Select APIs, click the dropdown menu.
4. Search for or scroll to find **Cloud Natural Language API**, and then check the box.
5. Click **OK**.
6. Click **Create**.
7. **Copy the generated API key**, and then click Close.

## Task 2. Set up Google Docs and call the Natural Language API
1. Create a new [*Google Docs document*](https://docs.google.com/document/create)
2. Click **Extension > App Script** replace all code in code.gs with the code in the lab. Replace *"your key here"* with **API Key** you created before.
3. Add text to your document. You can use the sample that comes from Charles Dickens' novel, [A Tale of Two Cities](https://www.gutenberg.org/cache/epub/98/pg98-images.html).
4. Reload the document to see new menu **Natural Language Tools**. Select text and then click the Mark Sentiment option from the Natural Language Tools menu.

## Task 3. Analyze syntax and parts of speech with the Natural Language API
1. Open **Navigation Menu > Compute Engine > VM Instance** click SSH on the VM list.
2. Create JSON file called `analyze-request.json`
```bash
cat << EOF > analyze-request.json
{
  "document":{
    "type":"PLAIN_TEXT",
    "content": "Google, headquartered in Mountain View, unveiled the new Android phone at the Consumer Electronic Show.  Sundar Pichai said in his keynote that users love their new Android phones."
  },
  "encodingType": "UTF8"
}
EOF
```
3. Call the Natural Language API Analyze Syntax
```bash
# replace .... with your API key
export API_KEY=....

curl "https://language.googleapis.com/v1/documents:analyzeSyntax?key=${API_KEY}" \
  -s -X POST -H "Content-Type: application/json" --data-binary @analyze-request.json > analyze-response.txt
```

## Task 4. Perform multilingual natural language processing
1. Still in the SSH to instance, create a JSON called `multi-nl-request.json`
```bash
cat << EOF > multi-nl-request.json
{
  "document":{
    "type":"PLAIN_TEXT",
    "content":"Le bureau japonais de Google est situé à Roppongi Hills, Tokyo."
  }
}
EOF
```
2. Call the Natural Language API Analyze Entities
```bash
curl "https://language.googleapis.com/v1/documents:analyzeEntities?key=${API_KEY}" \
  -s -X POST -H "Content-Type: application/json" --data-binary @multi-nl-request.json > multi-response.txt
```

## Congratulations!! 🎉🎉 