<script>
    import HeadingLink from "@/components/HeadingLink.svelte";
    import CodeBlock from "@/components/CodeBlock.svelte";
    import Toc from "@/components/Toc.svelte";
</script>

<div class="content">
    <p>
        Sometimes you may want to create or edit a collection programmatically (eg. as part of a DB
        migration). You could find all available Collection related db methods in the
        <a href="{import.meta.env.PB_GODOC_URL}/daos" target="_blank" rel="noopener noreferrer">daos godoc</a
        >, but here are some commonly used ones:
    </p>

    <Toc />

    <HeadingLink title="Fetch collections" />

    <HeadingLink title="Fetch collection by name or id" tag="h5" />
    <CodeBlock
        language="go"
        content={`
            collection, err := app.Dao().FindCollectionByNameOrId("example")
        `}
    />

    <HeadingLink title="Fetch collections by type" tag="h5" />
    <CodeBlock
        language="go"
        content={`
            baseCollections, err := app.Dao().FindCollectionsByType(models.CollectionTypeBase)

            authCollections, err := app.Dao().FindCollectionsByType(models.CollectionTypeAuth)
        `}
    />

    <HeadingLink title="Create new collection" />

    <HeadingLink title="Create new collection WITHOUT data validations" tag="h5" />
    <CodeBlock
        language="go"
        content={`
            import (
                "github.com/pocketbase/pocketbase/models"
                "github.com/pocketbase/pocketbase/models/schema"
                "github.com/pocketbase/pocketbase/tools/types"
            )

            ...

            collection := &models.Collection{
                Name:       "example",
                Type:       models.CollectionTypeBase,
                ListRule:   nil,
                ViewRule:   types.Pointer("@request.auth.id != ''"),
                CreateRule: types.Pointer(""),
                UpdateRule: types.Pointer("@request.auth.id != ''"),
                DeleteRule: nil,
                Schema:     schema.NewSchema(
                    &schema.SchemaField{
                        Name:     "title",
                        Type:     schema.FieldTypeText,
                        Required: true,
                        Unique:   true,
                        Options:  &schema.TextOptions{
                            Max: types.Pointer(10),
                        },
                    },
                    &schema.SchemaField{
                        Name:     "user",
                        Type:     schema.FieldTypeRelation,
                        Required: true,
                        Options:  &schema.RelationOptions{
                            MaxSelect:     types.Pointer(1),
                            CollectionId:  "ae40239d2bc4477",
                            CascadeDelete: true,
                        },
                    },
                ),
            }

            // the id is autogenerated, but you can set a specific one if you want to:
            // collection.SetId("...")
            // collection.MarkAsNew()

            if err := app.Dao().SaveCollection(collection); err != nil {
                return err
            }
        `}
    />

    <HeadingLink title="Create new collection WITH data validations" tag="h5" />
    <CodeBlock
        language="go"
        content={`
            import (
                "github.com/pocketbase/pocketbase/forms"
                "github.com/pocketbase/pocketbase/models"
                "github.com/pocketbase/pocketbase/models/schema"
                "github.com/pocketbase/pocketbase/tools/types"
            )

            ...

            collection := &models.Collection{}

            form := forms.NewCollectionUpsert(app, collection)
            form.Name = "example"
            form.Type = models.CollectionTypeBase
            form.ListRule = nil
            form.ViewRule = types.Pointer("@request.auth.id != ''")
            form.CreateRule = types.Pointer("")
            form.UpdateRule = types.Pointer("@request.auth.id != ''")
            form.DeleteRule = nil
            form.Schema.AddField(&schema.SchemaField{
                Name:     "title",
                Type:     schema.FieldTypeText,
                Required: true,
                Unique:   true,
                Options: &schema.TextOptions{
                    Max: types.Pointer(10),
                },
            })
            form.Schema.AddField(&schema.SchemaField{
                Name:     "user",
                Type:     schema.FieldTypeRelation,
                Required: true,
                Options: &schema.RelationOptions{
                    MaxSelect:     types.Pointer(1),
                    CollectionId:  "ae40239d2bc4477",
                    CascadeDelete: true,
                },
            })

            // validate and submit (internally it calls app.Dao().SaveCollection(collection) in a transaction)
            if err := form.Submit(); err != nil {
                return err
            }
        `}
    />

    <HeadingLink title="Update existing collection" />

    <HeadingLink title="Update collection WITHOUT data validations" tag="h5" />
    <CodeBlock
        language="go"
        content={`
            import (
                "github.com/pocketbase/pocketbase/models/schema"
            )

            ...

            collection, err := app.Dao().FindCollectionByNameOrId("example")
            if err != nil {
                return err
            }

            collection.Name = "example_update"
            collection.Schema.AddField(&schema.SchemaField{
                Name: "description",
                Type: schema.FieldTypeText,
            })

            if err := app.Dao().SaveCollection(collection); err != nil {
                return err
            }
        `}
    />

    <HeadingLink title="Update collection WITH data validations" tag="h5" />
    <CodeBlock
        language="go"
        content={`
            import (
                "github.com/pocketbase/pocketbase/forms"
                "github.com/pocketbase/pocketbase/models/schema"
            )

            ...

            collection, err := app.Dao().FindCollectionByNameOrId("example")
            if err != nil {
                return err
            }

            form := forms.NewCollectionUpsert(app, collection)
            form.Name = "example_update"
            form.Schema.AddField(&schema.SchemaField{
                Name: "description",
                Type: schema.FieldTypeText,
            })

            // validate and submit (internally it calls app.Dao().SaveCollection(collection) in a transaction)
            if err := form.Submit(); err != nil {
                return err
            }
        `}
    />

    <HeadingLink title="Delete collection" />
    <CodeBlock
        language="go"
        content={`
            collection, err := app.Dao().FindCollectionByNameOrId("example")
            if err != nil {
                return err
            }

            if err := app.Dao().DeleteCollection(collection); err != nil {
                return err
            }
        `}
    />
</div>
