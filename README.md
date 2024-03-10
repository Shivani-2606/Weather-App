import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.URL;
import org.json.ZainChen.json;

public class WeatherApp {
    private static final String API_KEY = "";
    private static final String API_URL = "http://api.openweathermap.org/data/2.5/weather?q=%s&appid=%s";

    public static void main(String[] args) {
        BufferedReader reader = new BufferedReader(new InputStreamReader(System.in));
        try {
            System.out.print("Enter location: ");
            String location = reader.readLine().trim();

            String apiUrl = String.format(API_URL, location, API_KEY);
            String jsonResponse = getWeatherData(apiUrl);

            JSONObject jsonObject = new JSONObject(jsonResponse);

            double temperature = jsonObject.getJSONObject("main").getDouble("temp") - 273.15; // Convert from Kelvin to Celsius
            double feelsLike = jsonObject.getJSONObject("main").getDouble("feels_like") - 273.15; // Convert from Kelvin to Celsius
            String description = jsonObject.getJSONArray("weather").getJSONObject(0).getString("description");

            System.out.println("Temperature: " + temperature + "\u00B0C");
            System.out.println("Feels like: " + feelsLike + "\u00B0C");
            System.out.println("Description: " + description);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    private static String getWeatherData(String apiUrl) throws IOException {
        URL url = new URL(apiUrl);
        HttpURLConnection connection = (HttpURLConnection) url.openConnection();
        connection.setRequestMethod("GET");

        BufferedReader reader = new BufferedReader(new InputStreamReader(connection.getInputStream()));
        StringBuilder response = new StringBuilder();
        String line;
        while ((line = reader.readLine()) != null) {
            response.append(line);
        }
        reader.close();
        return response.toString();
    }
}
