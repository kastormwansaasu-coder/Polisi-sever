import sdk from 'node-appwrite';

// Initialize Appwrite client
const client = new sdk.Client();
const databases = new sdk.Databases(client);

// Client setup from environment variables
client
  .setEndpoint(process.env.APPWRITE_ENDPOINT) // Example: https://[YOUR_PROJECT_ID].appwrite.io/v1
  .setProject(process.env.APPWRITE_PROJECT_ID) 
  .setKey(process.env.APPWRITE_API_KEY); 

// Define main function
export default async function (req, res) {
  try {
    const body = JSON.parse(req.payload || '{}');
    const { userId, youtubeLink } = body;

    if (!userId || !youtubeLink) {
      return res.json({ error: "User ID na YouTube Link lazima ziwepo" });
    }

    // Insert link into database
    const response = await databases.createDocument(
      process.env.APPWRITE_DATABASE_ID,    // Database ID
      process.env.APPWRITE_COLLECTION_ID, // Collection ID
      sdk.ID.unique(),                     // Document ID
      {
        userId,
        youtubeLink,
        redeemed: false,
        createdAt: new Date().toISOString()
      }
    );

    return res.json({ success: true, data: response });

  } catch (err) {
    console.error(err);
    return res.json({ error: err.message });
  }
}
