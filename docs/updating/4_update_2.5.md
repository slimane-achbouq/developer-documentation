# Updating from <2.5
    
If you are updating from a version prior to 2.4, you have implement all the changes from [Updating from <2.4](4_update_2.4.md) before following the steps below.

!!! note

    During database update, you have to go through all the changes between your current version and your final version
    **e.g. during update from v2.2 to v2.5 you have to perform all the steps from: <2.3, <2.4 and <2.5**.
    Only after applying all changes your database will work properly.

## Database update script

Apply the following database update script:

`mysql -u <username> -p <password> <database_name> < vendor/ezsystems/ezpublish-kernel/data/update/mysql/dbupdate-7.4.0-to-7.5.0.sql`

To update to v2.5.3, additionally run the following script:

`mysql -u <username> -p <password> <database_name> < vendor/ezsystems/ezpublish-kernel/data/update/mysql/dbupdate-7.5.2-to-7.5.3.sql`

## Changes to database schema

The introduction of [support for PostgreSQL](../guide/databases.md#using-postgresql) includes a change in the way database schema is generated.

It is now created based on [YAML configuration](https://github.com/ezsystems/ezpublish-kernel/blob/master/eZ/Bundle/EzPublishCoreBundle/Resources/config/storage/legacy/schema.yaml), using the new [`DoctrineSchemaBundle`](https://github.com/ezsystems/doctrine-dbal-schema).

If you are updating your application according to the usual procedure, no additional actions are required.
However, if you do not update your meta-repository, you need to take two additional steps:

- enable `EzSystems\DoctrineSchemaBundle\DoctrineSchemaBundle()` in `AppKernel.php`
- add [`ez_doctrine_schema`](https://github.com/ezsystems/ezplatform/blob/2.5/app/config/config.yml#L33) configuration

## Changes to Matrix Field Type

To migrate your content from legacy XML format to a new `ezmatrix` value use the following command:

```bash
bin/console ezplatform:migrate:legacy_matrix
```

## Required manual cache clearing if using Redis

If you are using Redis as your persistence cache storage you should always clear it manually after an upgrade.
You can do it in two ways, by using `redis-cli` and executing the following command:

```bash
FLUSHALL
```

or by executing the following command:

```bash
bin/console cache:pool:clear cache.redis
```

## Updating to 2.5.3

### Page builder

!!! enterprise

    This step is only **required when updating from versions higher than 2.2 and lower than 2.5.3**.
    In case of versions lower than 2.2, please skip this step or ignore the information that indexes from a script below already exist.
    
    When updating to v2.5.3, you need to run the following script to add missing indexes:
    
    ``` bash
    CREATE INDEX ezpage_map_zones_pages_zone_id ON ezpage_map_zones_pages(zone_id);
    CREATE INDEX ezpage_map_zones_pages_page_id ON ezpage_map_zones_pages(page_id);
    CREATE INDEX ezpage_map_blocks_zones_block_id ON ezpage_map_blocks_zones(block_id);
    CREATE INDEX ezpage_map_blocks_zones_zone_id ON ezpage_map_blocks_zones(zone_id);
    CREATE INDEX ezpage_map_attributes_blocks_attribute_id ON ezpage_map_attributes_blocks(attribute_id);
    CREATE INDEX ezpage_map_attributes_blocks_block_id ON ezpage_map_attributes_blocks(block_id);
    CREATE INDEX ezpage_blocks_design_block_id ON ezpage_blocks_design(block_id);
    CREATE INDEX ezpage_blocks_visibility_block_id ON ezpage_blocks_visibility(block_id);
    CREATE INDEX ezpage_pages_content_id_version_no ON ezpage_pages(content_id, version_no);
    ```
    
