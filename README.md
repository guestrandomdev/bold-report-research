# bold-report-research



https://help.boldreports.com/enterprise-reporting/developer-guide/how-to/generate-access-token-for-bold-reports-server-using-credentials/




``` html

<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Reports</title>

    <!-- Report Viewer component style -->
    <link
      href="https://cdn.boldreports.com/6.2.32/content/v2.0/tailwind-light/bold.report-viewer.min.css"
      rel="stylesheet"
    />

    <!-- Report Viewer component dependent scripts -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.6.0/jquery.min.js"></script>
    <script src="https://cdn.boldreports.com/6.2.32/scripts/v2.0/common/bold.reports.common.min.js"></script>
    <script src="https://cdn.boldreports.com/6.2.32/scripts/v2.0/common/bold.reports.widgets.min.js"></script>

    <!-- Report Viewer component script -->
    <script src="https://cdn.boldreports.com/6.2.32/scripts/v2.0/bold.report-viewer.min.js"></script>
  </head>
  <body style="margin: 0; padding: 0">
    <div style="height: 100vh" id="report-viewer"></div>
    <script type="text/javascript">
      $(function () {
        // Replace these with your actual tokens and endpoints
        var accessToken = 'your-initial-jwt-token';
        var refreshToken = 'your-refresh-token';
        var refreshUrl = '/api/refresh-token';

        // Function to check if the token is expired
        function isTokenExpired(token) {
          var payload = JSON.parse(atob(token.split('.')[1]));
          return payload.exp * 1000 < Date.now();
        }

        // Function to refresh the access token
        function refreshAccessToken() {
          return $.ajax({
            url: refreshUrl,
            method: 'POST',
            data: JSON.stringify({ token: refreshToken }),
            contentType: 'application/json',
            success: function (response) {
              accessToken = response.accessToken;
            },
            error: function () {
              alert('Failed to refresh token');
            }
          });
        }

        // Function to ensure valid access token
        function ensureValidAccessToken() {
          if (isTokenExpired(accessToken)) {
            return refreshAccessToken();
          } else {
            return $.Deferred().resolve();
          }
        }

        // Load the report viewer with the valid access token
        function loadReportViewer() {
          ensureValidAccessToken().done(function () {
            $("#report-viewer").boldReportViewer({
              reportPath: "sales-order-detail",
              reportServiceUrl: "/api/BoldReportsAPI",
              ajaxBeforeLoad: function (options) {
                options.headers = {
                  "Authorization": "Bearer " + accessToken
                };
              }
            });
          });
        }

        // Initialize the report viewer
        loadReportViewer();
      });
    </script>
  </body>
</html>



```
