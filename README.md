LaunchDarkly SDK for Java - DynamoDB integration
================================================

[![Circle CI](https://circleci.com/gh/launchdarkly/java-server-sdk-dynamodb.svg?style=shield)](https://circleci.com/gh/launchdarkly/java-server-sdk-dynamodb)
[![Javadocs](http://javadoc.io/badge/com.launchdarkly/launchdarkly-client.svg)](http://javadoc.io/doc/com.launchdarkly/launchdarkly-client-dynamodb-store)

This library provides a DynamoDB-backed persistence mechanism (feature store) for the [LaunchDarkly Java SDK](https://github.com/launchdarkly/java-client), replacing the default in-memory feature store.

This version of the library requires at least version 2.1 of the AWS SDK for Java, and at least version 4.6.0 of the LaunchDarkly Java SDK. The minimum Java version is 8 (because that is the minimum Java version of the AWS SDK 2.x). If you need to use Java 7, or if you are already using AWS SDK 1.x for some other purpose, you can use the 1.x releases of this library (which are developed on the "aws-v1" branch of the repository).

For more information, see also: [Using a persistent feature store](https://docs.launchdarkly.com/v2.0/docs/using-a-persistent-feature-store).

Quick setup
-----------

This assumes that you have already installed the LaunchDarkly Java SDK.

1. In DynamoDB, create a table which has the following schema: a partition key called "namespace" and a sort key called "key", both with a string type. The LaunchDarkly library does not create the table automatically, because it has no way of knowing what additional properties (such as permissions and throughput) you would want it to have.

2. Add this library to your project:

        <dependency>
          <groupId>com.launchdarkly</groupId>
          <artifactId>launchdarkly-client-dynamodb-store</artifactId>
          <version>2.0.0</version>
        </dependency>

3. If you do not already have the AWS SDK in your project, add the DynamoDB part of it. (This needs to be added separately, rather than being included in the LaunchDarkly jar, because AWS classes are exposed in the public interface.)

        <dependency>
          <groupId>software.amazon.awssdk</groupId>
          <artifactId>dynamodb</artifactId>
          <version>2.1.4</version>
        </dependency>

4. Import the LaunchDarkly package and the package for this library:

        import com.launchdarkly.client.*;
        import com.launchdarkly.client.dynamodb.*;

5. When configuring your SDK client, add the DynamoDB feature store:

        DynamoDbFeatureStoreBuilder store = DynamoDbComponents.dynamoDbFeatureStore("my-table-name")
            .caching(FeatureStoreCacheConfig.enabled().ttlSeconds(30));
        
        LDConfig config = new LDConfig.Builder()
            .featureStoreFactory(store)
            .build();
        
        LDClient client = new LDClient("YOUR SDK KEY", config);

By default, the DynamoDB client will try to get your AWS credentials and region name from environment variables and/or local configuration files, as described in the AWS SDK documentation. There are methods in `DynamoDBFeatureStoreBuilder` for changing the configuration options. Alternatively, if you already have a fully configured DynamoDB client object, you can tell LaunchDarkly to use that:

        DynamoDbFeatureStoreBuilder store = DynamoDbComponents.dynamoDbFeatureStore("my-table-name")
            .existingClient(myDynamoDbClientInstance);

Caching behavior
----------------

To reduce traffic to DynamoDB, there is an optional in-memory cache that retains the last known data for a configurable amount of time. This is on by default; to turn it off (and guarantee that the latest feature flag data will always be retrieved from DynamoDB for every flag evaluation), configure the store as follows:

        DynamoDbFeatureStoreBuilder store = DynamoDbComponents.dynamoDbFeatureStore("my-table-name")
            .caching(FeatureStoreCacheConfig.disabled());

For other ways to control the behavior of the cache, see `DynamoDbFeatureStoreBuilder.caching()`.

About LaunchDarkly
-----------

* LaunchDarkly is a continuous delivery platform that provides feature flags as a service and allows developers to iterate quickly and safely. We allow you to easily flag your features and manage them from the LaunchDarkly dashboard.  With LaunchDarkly, you can:
    * Roll out a new feature to a subset of your users (like a group of users who opt-in to a beta tester group), gathering feedback and bug reports from real-world use cases.
    * Gradually roll out a feature to an increasing percentage of users, and track the effect that the feature has on key metrics (for instance, how likely is a user to complete a purchase if they have feature A versus feature B?).
    * Turn off a feature that you realize is causing performance problems in production, without needing to re-deploy, or even restart the application with a changed configuration file.
    * Grant access to certain features based on user attributes, like payment plan (eg: users on the ‘gold’ plan get access to more features than users in the ‘silver’ plan). Disable parts of your application to facilitate maintenance, without taking everything offline.
* LaunchDarkly provides feature flag SDKs for
    * [Java](http://docs.launchdarkly.com/docs/java-sdk-reference "Java SDK")
    * [JavaScript](http://docs.launchdarkly.com/docs/js-sdk-reference "LaunchDarkly JavaScript SDK")
    * [PHP](http://docs.launchdarkly.com/docs/php-sdk-reference "LaunchDarkly PHP SDK")
    * [Python](http://docs.launchdarkly.com/docs/python-sdk-reference "LaunchDarkly Python SDK")
    * [Go](http://docs.launchdarkly.com/docs/go-sdk-reference "LaunchDarkly Go SDK")
    * [Node.JS](http://docs.launchdarkly.com/docs/node-sdk-reference "LaunchDarkly Node SDK")
    * [Electron](http://docs.launchdarkly.com/docs/electron-sdk-reference "LaunchDarkly Electron SDK")
    * [.NET](http://docs.launchdarkly.com/docs/dotnet-sdk-reference "LaunchDarkly .Net SDK")
    * [Ruby](http://docs.launchdarkly.com/docs/ruby-sdk-reference "LaunchDarkly Ruby SDK")
    * [iOS](http://docs.launchdarkly.com/docs/ios-sdk-reference "LaunchDarkly iOS SDK")
    * [Android](http://docs.launchdarkly.com/docs/android-sdk-reference "LaunchDarkly Android SDK")
* Explore LaunchDarkly
    * [launchdarkly.com](http://www.launchdarkly.com/ "LaunchDarkly Main Website") for more information
    * [docs.launchdarkly.com](http://docs.launchdarkly.com/  "LaunchDarkly Documentation") for our documentation and SDKs
    * [apidocs.launchdarkly.com](http://apidocs.launchdarkly.com/  "LaunchDarkly API Documentation") for our API documentation
    * [blog.launchdarkly.com](http://blog.launchdarkly.com/  "LaunchDarkly Blog Documentation") for the latest product updates
    * [Feature Flagging Guide](https://github.com/launchdarkly/featureflags/  "Feature Flagging Guide") for best practices and strategies

