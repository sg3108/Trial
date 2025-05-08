# Trial
private static final String DEV_BASE_URL = "https://dev-pingaccess.example.com/pa-admin-api/v3";
private static final String SIT_BASE_URL = "https://sit-pingaccess.example.com/pa-admin-api/v3";
private static final String DEV_AUTH_HEADER = "Bearer your-dev-token";
private static final String SIT_AUTH_HEADER = "Bearer your-sit-token";

public static void main(String[] args) throws IOException {
    int appId = 101; // Replace with actual Application ID
    String exportedAppJson = exportFromDev(appId);
    writeJsonToFile("exportedApp.json", exportedAppJson);
    String sitAppJson = transformForSIT(exportedAppJson);
    importToSIT(sitAppJson);
}

private static String exportFromDev(int appId) throws IOException {
    String url = DEV_BASE_URL + "/applications/" + appId;
    HttpURLConnection conn = (HttpURLConnection) new URL(url).openConnection();
    conn.setRequestMethod("GET");
    conn.setRequestProperty("Authorization", DEV_AUTH_HEADER);

    return readResponse(conn);
}

private static String transformForSIT(String json) {
    // Optional: Replace any dev-specific fields
    return json.replace("dev-hostname", "sit-hostname");
}

private static void importToSIT(String appJson) throws IOException {
    String url = SIT_BASE_URL + "/applications";
    HttpURLConnection conn = (HttpURLConnection) new URL(url).openConnection();
    conn.setRequestMethod("POST");
    conn.setRequestProperty("Authorization", SIT_AUTH_HEADER);
    conn.setRequestProperty("Content-Type", "application/json");
    conn.setDoOutput(true);

    try (OutputStream os = conn.getOutputStream()) {
        os.write(appJson.getBytes(StandardCharsets.UTF_8));
    }

    String response = readResponse(conn);
    System.out.println("SIT Response: " + response);
}

private static void writeJsonToFile(String fileName, String json) throws IOException {
    try (FileWriter file = new FileWriter(fileName)) {
        file.write(json);
    }
}

private static String readResponse(HttpURLConnection conn) throws IOException {
    try (BufferedReader in = new BufferedReader(
            new InputStreamReader(conn.getInputStream(), StandardCharsets.UTF_8))) {
        StringBuilder content = new StringBuilder();
        String line;
        while ((line = in.readLine()) != null) {
            content.append(line);
        }
        return content.toString();
    }
}

import java.io.*;
import java.net.HttpURLConnection;
import java.net.URL;
import java.nio.charset.StandardCharsets;

public class PingAccessPromotion {

    private static final String DEV_BASE_URL = "https://dev-pingaccess.example.com/pa-admin-api/v3";
    private static final String SIT_BASE_URL = "https://sit-pingaccess.example.com/pa-admin-api/v3";
    private static final String DEV_AUTH = "Bearer dev-access-token";
    private static final String SIT_AUTH = "Bearer sit-access-token";

    public static void main(String[] args) throws IOException {
        int devAppId = 123; // Replace with actual ID
        String devJson = fetchApplicationFromDev(devAppId);

        // Optional: Update JSON if needed (hostnames, secrets etc.)
        String transformedJson = devJson.replace("dev-host.com", "sit-host.com");

        postApplicationToSIT(transformedJson);
    }

    private static String fetchApplicationFromDev(int appId) throws IOException {
        URL url = new URL(DEV_BASE_URL + "/applications/" + appId);
        HttpURLConnection conn = (HttpURLConnection) url.openConnection();
        conn.setRequestMethod("GET");
        conn.setRequestProperty("Authorization", DEV_AUTH);

        return readResponse(conn);
    }

    private static void postApplicationToSIT(String appJson) throws IOException {
        URL url = new URL(SIT_BASE_URL + "/applications");
        HttpURLConnection conn = (HttpURLConnection) url.openConnection();
        conn.setRequestMethod("POST");
        conn.setRequestProperty("Authorization", SIT_AUTH);
        conn.setRequestProperty("Content-Type", "application/json");
        conn.setDoOutput(true);

        try (OutputStream os = conn.getOutputStream()) {
            os.write(appJson.getBytes(StandardCharsets.UTF_8));
        }

        System.out.println("SIT Response: " + readResponse(conn));
    }

    private static String readResponse(HttpURLConnection conn) throws IOException {
        try (BufferedReader reader = new BufferedReader(
                new InputStreamReader(conn.getInputStream(), StandardCharsets.UTF_8))) {
            StringBuilder response = new StringBuilder();
            String line;
            while ((line = reader.readLine()) != null) {
                response.append(line);
            }
            return response.toString();
        }
    }
}


