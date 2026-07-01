Frontend(Reverse Proxy)

1) install URL Rewrite Module 2.1
2) install Application Request Routing Cache 3
    - (Checked) Enable proxy
3) create Web.config or rules (rewrite url)

for example (Angular)

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <rewrite>
      <rules>

        <!-- match frontend call backend -->
        <rule name="Proxy to JSONPlaceholder API" stopProcessing="true">
          <!-- when path start with "/api" -->
          <!-- call: http://localhost:4200/api/todos -->
          <!-- call-rewite-to: https://jsonplaceholder.typicode.com/todos -->
          <!-- {R:1} is (.*) -->
          <match url="^api/(.*)" />
          <action type="Rewrite" url="https://jsonplaceholder.typicode.com/{R:1}" />
        </rule>

        <!-- match frontend router only -->
        <rule name="Angular Routes" stopProcessing="true">
          <match url=".*" />
          <conditions logicalGrouping="MatchAll">
            <add input="{REQUEST_FILENAME}" matchType="IsFile" negate="true" />
            <add input="{REQUEST_FILENAME}" matchType="IsDirectory" negate="true" />
          </conditions>
          <action type="Rewrite" url="/index.html" />
        </rule>

      </rules>
    </rewrite>
  </system.webServer>
</configuration>
```
