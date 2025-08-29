To update all sequences in PostgreSQL run this script. That's all ;)




		DO $$
		DECLARE
		    r RECORD;
		BEGIN
		    FOR r IN (
		        SELECT
					o.nspname AS nSchema, p.relname AS nTable, t.attname AS nColumn, a.relname AS nSequence
		        FROM
		            pg_class a
		        JOIN
		            pg_depend c ON a.oid = c.objid
		        JOIN
		            pg_class p ON c.refobjid = p.oid
		        JOIN
		            pg_attribute t ON p.oid = t.attrelid AND c.refobjsubid = t.attnum
				JOIN 
					pg_namespace o ON o.oid = a.relnamespace
		        WHERE
		            a.relkind = 'S' AND c.deptype = 'a' AND p.relkind = 'r'
		
		    ) LOOP
		
				EXECUTE format('SELECT setval(''%I.%I'', (SELECT MAX(id) FROM %I.%I));', r.nSchema, r.nSequence, r.nSchema, r.nTable);
		
				RAISE NOTICE 'SELECT setval(''%.%'', (SELECT MAX(id) FROM "%"."%"));', r.nSchema, r.nSequence, r.nSchema, r.nTable;
		
		    END LOOP;
			
		END $$;
