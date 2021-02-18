# @backstage/plugin-cmdb

A frontend for [CMDB-service](https://github.com/seizadi/cmdb), this plugin allows you to 
applications that are managed by CMDB backend.

This is an experimental plugin to understand how to integrate with
backstage echo system.

## Introduction


## Getting Started

To get started, you will need a running instance 
of [`CMDB-service`](https://github.com/seizadi/cmdb).
It's likely you will need to [enable CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) 
to integrate with Backstage. I am hoping that I can use the
backend proxy and avoid this need.

When you have an instance running that Backstage can hook into, 
first install the plugin into your app:

***For now I am building the plugin into the tree, later I will play
around with building it as a package and try the following:***
```sh
$ yarn add @backstage/plugin-cmdb
```

Then make sure to export the plugin in
your app's [`plugins.ts`](https://github.com/backstage/backstage/blob/master/packages/app/src/plugins.ts)
to enable the plugin:

```js
export { plugin as CmdbPlugin } from 'cmdb-plugin';
```

So for now since I don't have the package I will reference it directly
and see if things work:
```js
export { plugin as CmdbPlugin } from '../../../plugins/cmdb/src';
```

Modify your app routes in `App.tsx` to include the Router component exported from the plugin, for example:

Similarly reference the CMDB Router directly:
```js
export { Router as CmdbRouter } from '../../../plugins/cmdb/src';
```
```tsx
// At the top imports
import { Router as LighthouseRouter } from '@backstage/plugin-lighthouse';

// Inside App component
<Routes>
  // ...
  <Route path="/cmdb/*" element={<CmdbRouter />} />
  // ...
</Routes>;
```

Then configure the `cmdb-service` URL in your [`app-config.yaml`](https://github.com/backstage/backstage/blob/master/app-config.yaml).

```yaml
cmdb:
  baseUrl: http://your-service-url
```

## Integration with the Catalog

The Lighthouse plugin can be integrated into the catalog so that Lighthouse audit information relating to a component
can be displayed within that component's entity page. In order to link an Entity to its Lighthouse audits, the entity
must be annotated as follows:

```yaml
apiVersion: backstage.io/v1alpha1
kind: Component
metadata:
  # ...
  annotations:
    # ...
    lighthouse.com/website-url: # A single website url e.g. https://backstage.io/
```

> NOTE: The plugin only supports one website URL per component at this time.

Add a **Cmdb tab** to the EntityPage:

Similarly reference the CMDB Router directly:
```js
export { EmbeddedRouter as CmdbRouter } from '../../../plugins/cmdb/src';
````

```tsx
// packages/app/src/components/catalog/EntityPage.tsx
import { EmbeddedRouter as CmdbRouter } from '@backstage/plugin-cmdb';

// ...
const WebsiteEntityPage = ({ entity }: { entity: Entity }) => (
  <EntityPageLayout>
    // ...
    <EntityPageLayout.Content
      path="/lighthouse/*"
      title="Lighthouse"
      element={<LighthouseRouter entity={entity} />}
    />
  </EntityPageLayout>
);
```

> NOTE: The embedded router renders page content without a header section allowing it to be rendered within a
> catalog plugin page.

Add a **Lighthouse card** to the overview tab on the EntityPage:

```tsx
// packages/app/src/components/catalog/EntityPage.tsx
import {
  LastCmdbhouseAuditCard,
  isPluginApplicableToEntity as isCmdbAvailable,
} from '@backstage/plugin-lighthouse';

// ...

const OverviewContent = ({ entity }: { entity: Entity }) => (
  <Grid container spacing={3}>
    // ...
    {isCmdbAvailable(entity) && (
      <Grid item sm={4}>
        <LastCmdbAuditCard />
      </Grid>
    )}
  </Grid>
);
```

## Development

```bash
npm publish
```
