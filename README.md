# service-uptime-app
outline of how you could build a service uptime app:
1.Create a database to store the URLs that the user enters. You could use a NoSQL database like MongoDB to store the URLs and the status of each endpoint (UP or DOWN).
2.Write a script that retrieves the URLs from the database and pings each endpoint at a specific interval (e.g. every 5 minutes). You could use the requests library in Python to send an HTTP request to each endpoint and check the status code of the response. If the status code is in the 2xx range, mark the endpoint as UP. Otherwise, mark it as DOWN.
3.Create a simple user interface (UI) for the app. This could be a web page that displays the current status of all endpoints in a table or list. You could use a frontend framework like React or Vue.js to build the UI.
4.Set up a server to host the app. You could use a platform like Heroku to host the server and run the script that checks the endpoint status.
5.Deploy the app and test it to make sure it is working as expected. You could use a tool like Postman to manually send requests to the endpoints and check the status.


Java code for a service uptime app
Stores a list of URLs in a List object.
Pings each URL at a specific interval (every 5 minutes) using the java.net.HttpURLConnection class.
Prints the status of each URL to the console.
import java.net.HttpURLConnection;
import java.net.URL;
import java.util.List;
import java.util.concurrent.TimeUnit;

public class ServiceUptimeApp {

    public static void main(String[] args) {
        // Create a list of URLs to check
        List<String> urls = List.of(
                "https://www.google.com",
                "https://www.youtube.com",
                "https://www.github.com"
        );

        // Check the status of each URL every 5 minutes
        while (true) {
            for (String url : urls) {
                checkUrlStatus(url);
            }
            try {
                TimeUnit.MINUTES.sleep(5);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    private static void checkUrlStatus(String urlString) {
        try {
            URL url = new URL(urlString);
            HttpURLConnection connection = (HttpURLConnection) url.openConnection();
            connection.setRequestMethod("GET");
            connection.connect();

            int code = connection.getResponseCode();
            if (code >= 200 && code < 300) {
                System.out.println(urlString + " is UP");
            } else {
                System.out.println(urlString + " is DOWN");
            }
        } catch (Exception e) {
            System.out.println(urlString + " is DOWN");
        }
    }
}

code that store the URLs entered by users in a MongoDB database using the Java MongoDB Driver:


import com.mongodb.client.MongoClient;
import com.mongodb.client.MongoClients;
import com.mongodb.client.MongoCollection;
import com.mongodb.client.MongoDatabase;
import org.bson.Document;

public class Database {

    private static final String DATABASE_NAME = "service_uptime";
    private static final String COLLECTION_NAME = "urls";

    private static MongoClient mongoClient;
    private static MongoDatabase database;
    private static MongoCollection<Document> collection;

    static {
        // Connect to the database
        mongoClient = MongoClients.create("mongodb://localhost:27017");
        database = mongoClient.getDatabase(DATABASE_NAME);
        collection = database.getCollection(COLLECTION_NAME);
    }

    public static void addUrl(String url) {
        // Create a document to store the URL
        Document document = new Document("url", url);
        // Insert the document into the collection
        collection.insertOne(document);
    }

    public static List<String> getUrls() {
        // Query the collection for all documents
        List<Document> documents = collection.find().into(new ArrayList<>());
        // Extract the URLs from the documents and return them as a list
        return documents.stream()
                .map(d -> d.getString("url"))
                .collect(Collectors.toList());
    }
}

code that simple user interface (UI) for your service uptime app using JavaScript and the React library:


import React, { useState, useEffect } from "react";
function App() {
  const [urls, setUrls] = useState([]);
  const [status, setStatus] = useState({});

  useEffect(() => {
    // Fetch the list of URLs from the server
    fetch("/api/urls")
      .then(response => response.json())
      .then(data => setUrls(data.urls));

    // Periodically fetch the status of each URL
    const interval = setInterval(() => {
      fetch("/api/status")
        .then(response => response.json())
        .then(data => setStatus(data.status));
    }, 5000);

    // Clear the interval when the component unmounts
    return () => clearInterval(interval);
  }, []);

  // Render a table showing the status of each URL
  return (
    <table>
      <thead>
        <tr>
          <th>URL</th>
          <th>Status</th>
        </tr>
      </thead>
      <tbody>
        {urls.map(url => (
          <tr key={url}>
            <td>{url}</td>
            <td>{status[url] || "PENDING"}</td>
          </tr>
        ))}
      </tbody>
    </table>
  );
}

export default App;
